# 客户订单

## 模块定位
- 对应表：`wh_customer_order`
- 子表：`wh_customer_order_detail`、`wh_customer_order_item`
- 入口主链：`CustomerOrderServiceImpl`、`CustomerOrderIntentBusiness`
- 控制层入口分散在：
  - `CustomerOrderIntentController`
  - `CustomerOrderReceiveController`
- 这不是单纯销售订单表，而是“现场挑货订单 + 客留存订单 + 小程序/平台订单 + 对外履约宿主 + 同步状态宿主”的复合主链。

## 主数据职责
- 单头关键字段包括：
  - `orderNo`
  - `orderName`
  - `outOrderNo`
  - `outOrderSort / outOrderType`
  - `outTaskId`
  - `outPpOrderNo`
  - `orderStatus`
  - `outOrderStatus`
  - `syncId / syncStatus`
  - `deliveryNotify`
  - `shipGoldWeight`
  - `pickTotalNum / pickTotalGoldWeight`
  - `staffStatus / tellerSettleStatus`
- 它同时承接三层状态：
  - 内部订单状态 `orderStatus`
  - 外部平台状态 `outOrderStatus`
  - 对外同步状态 `syncStatus`
- 再加上业务员/柜员视角状态，说明这张表本质是订单域宿主，不是单一审批表。

## 建单来源
- 现场挑货、客留存、小程序和平台订单都会汇入 `wh_customer_order`。
- 老庙接口订单走 `createLmCustomerOrder(form)`：
  - 先按 `outOrderNo + customerId` 防重
  - 生成内部 `orderNo`
  - 业务类型写成 `SO_CUSTOMER_PLATFORM`
  - 默认进入 `WAIT_PICKING`
  - 回写外部单号、任务号、订单类型、订单分类
  - 按老庙传入模号逐行生成 `customer_order_detail`
- 现场挑货走 `CustomerOrderIntentBusiness.siteChoose(...)`：
  - 建单
  - 锁单件
  - 写 `customer_order_item`
  - 写 `customer_order_detail`
  - 写单件日志

## 两套状态链
- `orderStatus` 是内部履约/作业状态，注释口径大致包括：
  - 未提交
  - 待接单
  - 待拣货
  - 待回货
  - 已作废
  - 待开单
  - 开单中
  - 已开单
  - 已发货
- `outOrderStatus` 是外部平台状态，注释口径大致包括：
  - 待接单
  - 部分取消
  - 已取消
  - 接单中
  - 已接单
  - 发货中
  - 已发货
  - 发货失败
- 这两套状态并行存在，且并不总是同步推进。

## 内部执行链
- 挑货链由 `CustomerOrderIntentBusiness` 承接：
  - 现场挑货时锁单件、建 item 子表、建 detail 子表
  - 作废、清拣货时解锁单件并落日志
  - 回货确认会把订单从 `WAIT_COMEBACK` 推回 `WAIT_PICKING`
- 出货确认 `delivered(...)`：
  - 只针对老庙客户平台订单
  - 对每张销售结算单触发 `syncTaskService.deliveredOrder(...)`
  - 把 `saleBill.syncStatus` 切成同步中/失败
  - 把订单切到 `orderStatus=DELIVERED`、`outOrderStatus=ON_DELIVERY`
  - 对 PP 单要求 `outPpOrderNo`

## 出货回写链
- 真正外部履约成功不是在订单模块里完成，而是由 `deliverybill` 回调链反向改订单：
  - `orderStatus -> DELIVERED`
  - `outOrderStatus -> SHIPPED`
  - `deliveryNotify`
  - `outPpOrderNo`
- 所以客户订单既是出货来源宿主，也是出货回写宿主。

## 同步与接口链
- 老庙协议层入口：
  - `createLmCustomerOrder(...)`
  - `lmCustomerOrderStateChange(...)`
  - `lmCustomerOrderPpNotify(...)`
- 订单上直接存：
  - `outOrderNo`
  - `outTaskId`
  - `outPpOrderNo`
  - `syncId / syncStatus`
  - `deliveryNotify`
- `CustomerOrderIntentBusiness` 还可以反查：
  - 共享流水 `getApiRecords(orderNo)`
  - 同步任务 `getTaskList(orderNo, bizType)`
  - 老庙接口痕迹 `getApiTransferList(orderNo)`

## 关联模块
- `deliverybill`：订单下游履约宿主。
- `salebill`：订单开单/结算结果宿主。
- `pack`：订单备货、开单前置宿主。
- `synctask`：对外同步任务宿主。
- `lmapitransfer`：老庙接口痕迹宿主。
- `shareflowrecord`：订单出货后的共享库存同步流水。
- `customersku / skucustomerlm / skumodel`：平台订单建单时的款式、模号来源。

## 关键结论
- 客户订单不是简单销售订单，而是整个客户履约域宿主。
- 它同时承接：
  - 内部拣货/开单/发货状态
  - 外部平台状态
  - 对外同步状态
  - 出货通知摘要
  - 现场单件锁定与释放结果
- 老庙链里“建单、出货、回调、共享库存同步”最终都会回落到这张表上的某组字段。

## 风险与疑点
- `⚠️` 订单同时维护 `orderStatus / outOrderStatus / syncStatus / staffStatus / tellerSettleStatus` 五组状态，改造时非常容易只改一层。
- `⚠️` `createLmCustomerOrder(...)` 里 `shopId` 仍有默认 `1010L` 回退，网点口径需要继续确认。
- `⚠️` `delivered(...)` 先把订单切到 `DELIVERED + ON_DELIVERY`，而真正外部成功还要等 `deliverybill` 回调，状态命名有前后语义重叠。
- `⚠️` 订单对外链路信息散落在 `wh_customer_order`、`wh_sync_task`、`wh_lm_api_transfer`、`wh_delivery_bill` 多表里，排障必须按链路串看，不能只看单头。
