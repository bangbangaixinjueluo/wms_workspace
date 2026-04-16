# 出货单

## 模块定位
- 对应表：`wh_delivery_bill`
- 明细表：`wh_delivery_detail`
- 入口：`DeliveryBillController`、`DeliveryBillServiceImpl`、`DeliveryBusiness`
- 这不是普通发货登记单，而是“销售结算下游履约宿主 + 老庙出货推送 + 回调写回 + 共享库存延迟扣减”的复合执行链。

## 单据来源
- 出货单不是人工主建，主来源在 `SaleBillBusiness.genDeliveryBill(...)`。
- 只有客户平台订单结算才会生成出货单；成品结算单、结价单不会生成。
- 生成时会按 `customerOrderNo` 聚合 `saleDetail`：
  - 生成单头 `wh_delivery_bill`
  - 复制销售明细到 `wh_delivery_detail`
  - 汇总 `totalNum / totalGoldWeight`
- 出货单因此是“销售结算履约分单”的结果，不是独立销售单。

## 主数据职责
- 单头关键字段包括：
  - `billNo`
  - `billType`
  - `bizType`
  - `status`
  - `customerOrderNo`
  - `saleBillNo`
  - `trackingNo`
  - `deliveryTime`
  - `deliveryMan`
  - `serialNumber`
- 明细关键字段包括：
  - `itemId / itemNo / epc`
  - `skuId / skuNo`
  - `modelNo / specs`
  - `weight / goldWeight / grossWeight`
  - `saleCmtFee`

## 状态主链
- 注释口径显示单头状态大致为：
  - `0` 待出货
  - `1` 出货中
  - `2` 已出货
  - `3` 出货失败
  - `4` 作废
- `ship(id, trackingNo)` 只允许从“待出货”进入。
- `reship(id, trackingNo)` 只允许从“出货失败”进入。
- 老庙回调成功后才会从“出货中/失败”落到“已出货”。

## 出货执行链
- `ship(id, trackingNo)` 的核心动作是：
  - 校验出货单存在且为待出货
  - 反查客户订单 `customerOrderNo`
  - 若是 PP 单，强制要求物流单号，并回写客户订单 `outPpOrderNo`
  - 生成 `wh_lm_api_transfer` 调用痕迹
  - 按订单类型组装老庙请求报文
  - 调 `lmHttpOrderService.pushYxysOrder(...)` 或 `pushPPOrder(...)`
  - 保存接口痕迹
  - 根据接口即时结果把出货单切成“出货中”或“出货失败”
- 即时成功并不代表业务完结，真正履约完成依赖后续老庙回调。

## 回调写回链
- `callbackYxysOrder(...)` 和 `callbackPPOrder(...)` 会同时反查：
  - 出货单
  - 接口痕迹 `wh_lm_api_transfer`
  - 客户订单
  - 销售结算单
- 回调成功时会同时改：
  - `deliveryBill.status -> DELIVERY_SUCCESS`
  - `customerOrder.orderStatus -> DELIVERED`
  - `customerOrder.outOrderStatus -> SHIPPED`
  - `customerOrder.deliveryNotify`
  - `saleBill.syncStatus -> ONGOING`
  - `lm_api_transfer.callback*`
- 回调失败时会同时改：
  - `deliveryBill.status -> DELIVERY_FAIL`
  - `customerOrder.outOrderStatus -> DELIVERY_FAIL`
  - `customerOrder.deliveryNotify`
  - `saleBill.syncStatus -> FAIL`
  - `lm_api_transfer.callback*`

## 共享库存延迟扣减
- 出货回调成功后，不是立即同步共享库存，而是先 `lmStockSyncDelayQueue.add(deliveryBill.getBillNo())`。
- `LmStockSyncDelayQueue` 会延迟约 60 秒消费：
  - 反查出货单和明细
  - 按 `modelNo + specs` 聚合件数
  - 反查新共享链 `wh_share_stock`
  - 生成 `wh_lm_api_transfer` 新痕迹
  - 生成 `wh_share_flow_record`
  - 调老庙“订单出入库”接口
  - 成功后累计回写 `wh_share_stock`
- 所以“出货成功”后面还有一条异步共享库存扣减侧链。

## 关联模块
- `salebill`：出货单的直接来源。
- `customerorder`：履约状态写回宿主。
- `lmapitransfer`：出货推送和共享库存扣减的接口痕迹宿主。
- `synctask`：老庙协议层历史回调宿主，但出货履约当前主要落在 `DeliveryBusiness`。
- `sharestock / shareflowrecord`：出货成功后的共享库存扣减链。

## 关键结论
- 出货单不是简单物流单，而是客户平台订单履约宿主。
- 它的真实业务闭环是：
  - 销售结算生成出货单
  - 出货单推送老庙
  - 老庙回调改出货/订单/结算状态
  - 成功后再异步扣减共享库存

## 风险与疑点
- `⚠️` `ship(...)` 只根据接口即时结果把单据切到“出货中/失败”，真正成功依赖回调；如果回调丢失，单据可能长期停在中间态。
- `⚠️` `callbackYxysOrder(...)` / `callbackPPOrder(...)` 里 `callbackParams` 保存的是整批 `form`，不是当前行数据，单笔痕迹回看时噪音较大。
- `⚠️` 回调成功后把 `saleBill.syncStatus` 设成 `ONGOING` 而不是明确“成功”，说明后面还挂着共享库存异步链，口径容易被误读。
- `⚠️` 延迟队列按 `modelNo + specs` 聚合扣减共享库存，如果同模号规格跨多个共享库存维度并存，归集口径需要继续确认。
