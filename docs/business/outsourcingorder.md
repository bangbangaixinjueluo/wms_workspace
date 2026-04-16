# 委外加工订单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
委外加工订单不是单纯的“发外加工单”主表，而是委外加工主链宿主。它在建单、修改、作废、终止核销时直接回写需求单累计；在审核后承接 BOM 验收、工厂回复、逾期提醒；在下游又被订单核销、单件入库、转成品入库持续反向改写。

从代码真实行为看，它的核心职责有 4 类：
1. 管理委外下单主档和明细。
2. 把委外件数、回复交期回写到需求单。
3. 承接订单核销结果，重算“待核销/部分核销/核销完毕/终止核销”状态。
4. 向单件入库、成品入库、逾期提醒等下游输出待处理池。

## 关键入口
- Controller: [OutsourcingOrderController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/outsourcingorder/OutsourcingOrderController.java)
- Service: [OutsourcingOrderServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/outsourcingorder/OutsourcingOrderServiceImpl.java)
- 明细 Service: [OutsourcingOrderDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/outsourcingorderdetail/OutsourcingOrderDetailServiceImpl.java)
- BPM API: [OutsourcingOrderApiImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/api/OutsourcingOrderApiImpl.java)
- 状态枚举: [OutSourcingOrderStatusEnum.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-api/src/main/java/cn/iocoder/yudao/module/wh/enums/basic/order/OutSourcingOrderStatusEnum.java)
- Mapper SQL: [OutsourcingOrderMapper.xml](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/resources/mapper/outsourcingorder/OutsourcingOrderMapper.xml)

## 核心数据
### 1. 单头 `wh_outsourcing_order`
关键字段：
1. `orderStatus`：主状态。
2. `receiveStatus`：BOM/验收侧接收状态。
3. `processResult/processUserId/processInstanceId`：审批流程痕迹。
4. `totalNum/totalGramWeight`：委外总件数、总重。
5. `totalDeliveryNum/totalDeliveryGramWeight`：累计核销回货量。
6. `totalFinishNum`：累计终止核销量。
7. `deliveryTime/replyDeliveryDate/writeoffDate/overdueDays`：交期与逾期字段。

### 2. 明细 `wh_outsourcing_order_detail`
关键字段：
1. `demandOrderId/demandDetailId`：来源需求单挂接。
2. `outsourcingNum`：本次委外件数。
3. `sumGramWeight`：预计总重。
4. `totalWriteoffNum/totalWriteoffGramWeight`：累计核销量。
5. `totalPassNum/totalPassGramWeight`：累计合格核销量。
6. `totalBadNum/totalBadGramWeight`：累计不合格量。
7. `totalReturnNum/totalReturnWeight`：累计回货量。
8. `finishNum/finishReason/finishDate`：终止核销字段。
9. `replyDeliveryDate/factoryAnswer/overdueDays`：工厂回复与逾期字段。
10. `convertInboundNo`：是否已转成品入库。

## 真实业务主链
### 1. 建单/改单
`createOutsourcingOrder(...)` 与 `updateOutsourcingOrder(...)` 的真实动作不是只写主表：
1. 先处理明细、校验需求单存在且允许委外。
2. 按明细最小交货日回写单头 `deliveryTime`。
3. 按明细最大回复交期回写单头 `replyDeliveryDate`。
4. 调 `outsourceDemandOrder(detailList, true)`，把需求单明细 `producingNum` 累加。
5. 如果明细带回复交期，还把需求单明细 `replyDate/overdueDays` 一起改掉。
6. 改单前会先把旧明细对应需求单累计量整条回滚，再按新明细重新写入。

因此委外单本质上是“需求单委外占用链”的执行单。

### 2. 审核
`confirm(Long billId)` 的主动作：
1. 只允许未审/反审态审核。
2. 校验单头交货日不为空。
3. 校验每条明细 `outsourcingNum > 0`，且 `deliveryTime/replyDeliveryDate` 不能为空。
4. 调 `checkBom(...)` 判断是否需要供应商 BOM 接收链。
5. 单头状态改为 `CONFIRM`。
6. 写入 `approvedMan/approvedTime`。
7. `receiveStatus` 按是否需要接收分别置为 `UN_RECEIVE` 或 `NONE`。

`batchConfirm(...)` 是同一条逻辑的批量版，按订单逐张校验后批量更新。

### 3. 反审
`infirm(Long billId)` 只允许 `CONFIRM` 反审，但有两条硬阻断：
1. `totalDeliveryNum >= 1` 不能反审。
2. `totalFinishNum >= 1` 不能反审。

也就是说，只要委外单已经发生核销或终止核销，就不能再回到反审态。

### 4. 作废
`discard(Long billId)` 的真实动作是：
1. 禁止对已审核单据作废。
2. 读取全部委外明细。
3. 调 `outsourceDemandOrder(detailList, false)`，把需求单侧 `producingNum/replyDate` 占用回滚。
4. 单头状态改成 `DISCARD`。

因此作废不是简单改单头，而是会把需求单占用量整条释放。

### 5. 终止核销
`finish(FinishWriteOffReqVO reqVO)` 是委外单的关键业务动作之一：
1. 只允许在 `CONFIRM` 或 `CANCELLATION` 状态执行。
2. 可按整单或按指定明细终止。
3. 已有 `finishNum > 0` 的明细不会重复终止。
4. `finishOutsourcing(...)` 会把“未核销量”写成终止量：
   - `cancelNum = outsourcingNum - totalWriteoffNum`
   - 需求单明细 `cancelNum += cancelNum`
   - 写 `cancelDate`
   - 委外明细写 `finishNum/finishReason/finishDate`
5. 之后按全量明细重算单头 `totalFinishNum` 和 `orderStatus`。

状态重算规则由 `computeFinishStatus(...)` 控制：
1. 全部 `outsourcingNum <= finishNum`：`FINISH`
2. 全部 `outsourcingNum <= totalWriteoffNum + finishNum`：`DELIVERED`
3. 任意明细 `totalWriteoffNum > 0`：`CANCELLATION`
4. 否则回到 `CONFIRM`

所以“终止核销”不是附属标记，而是参与委外单主状态聚合的正式分支。

## 下游挂接
### 1. 订单核销
订单核销审核/反审会持续回写委外单明细：
1. `totalWriteoffNum/totalWriteoffGramWeight`
2. `totalPassNum/totalPassGramWeight`
3. `totalBadNum/totalBadGramWeight`
4. `totalReturnNum/totalReturnWeight`
5. `writeoffDate`

然后再重算单头 `totalDeliveryNum/totalDeliveryGramWeight/totalFinishNum/orderStatus`。因此委外单“待核销/部分核销/核销完毕”不是自己维护，而是由核销链反推。

### 2. 单件入库
`waitEntryPage` 不是看委外总量，而是看：
1. 委外单状态必须在 `('7','8')`，也就是部分核销或核销完毕。
2. `total_delivery_num - 已转单件入库数量 > 0`

这里“已转单件入库数量”来自 `wh_entry_bill_detail + wh_entry_bill` 聚合。说明委外单和单件入库之间是“核销回货量 -> 待入库池 -> 单件入库消耗池”的关系。

### 3. 转成品入库
明细侧 `inboundOptions(...)` 只开放：
1. 父单状态为 `DELIVERED`
2. `convertInboundNo IS NULL`

也就是说，只有“核销完毕且未转过成品入库”的委外明细，才可进入转成品入库来源池。

### 4. 逾期提醒
`handleOverdue()` 会持续刷新未完成明细的 `overdueDays`，`NotifyBusiness.handleOutsourcingOrderOverdueWarning(orderNo)` 再按单头 `totalNum - totalDeliveryNum` 给创建人发逾期提醒。

### 5. 工厂回复
`factoryAnswer(...)` 只是把回复文本回写到指定委外明细 `factoryAnswer`，不改变主状态。

## 字段与口径
### 1. 委外重量口径
`calcSumGramWeight(...)` 当前使用：
1. `(minGramWeight + maxGramWeight) / 2`
2. 再乘 `demandNum`

也就是说，`sumGramWeight` 不是乘 `outsourcingNum`，而是乘来源需求件数。

### 2. 状态口径
`OutSourcingOrderStatusEnum` 当前可见主状态：
1. `INIT(0)`：未委外/草稿
2. `CONFIRM(2)`：待核销
3. `INFIRM(3)`：已反审
4. `DISCARD(4)`：已作废
5. `CANCELLATION(7)`：部分核销
6. `DELIVERED(8)`：核销完毕
7. `FINISH(9)`：终止核销

其中：
1. `getToWriteOffList()` 只允许 `CONFIRM/CANCELLATION` 进入核销池。
2. `getToEntryBill()` 只允许 `CANCELLATION/DELIVERED` 进入单件入库可见池。

## BPM
`OutsourcingOrderApiImpl.updateProcessResult(...)` 已对接到 service，但当前 `updateProcessResult(...)` 实现只做两件事：
1. 回写 `processResult`
2. 审批通过时补 `approvedMan/approvedTime`

没有看到它直接驱动 `orderStatus`、`receiveStatus` 或需求单/核销链动作，所以 BPM 在这个模块里更像“流程痕迹回写”，不是主状态机入口。

## 风险与待确认
1. `calcSumGramWeight(...)` 用的是 `demandNum` 而不是 `outsourcingNum`，预计总重可能与实际委外件数口径不一致。
2. `discard(...)` 只显式禁止 `CONFIRM`，未看到对 `CANCELLATION/DELIVERED/FINISH` 的统一状态白名单校验。
3. `updateProcessResult(...)` 只写 `processResult` 和审批人，不驱动主业务状态，需确认 BPM 是否只是痕迹字段。
4. `OutsourcingOrderMapper.xml#selectWaitEntryPage` 供应商过滤条件写的是 `b.supplier_id`，但当前 SQL 主表别名实际是 `a`，需确认该筛选是否已失效。
5. `inboundOptions(...)` 只按 `convertInboundNo IS NULL + orderStatus=DELIVERED` 控来源，未见校验“剩余可转数量 > 0”。

## 结论
委外加工订单的真实定位应当是“需求单委外占用 + 核销聚合宿主 + 入库来源池”。它不是收尾单，而是贯穿需求、委外、核销、入库、逾期提醒整条链路的中间主干单据。后续只要改订单核销、单件入库、转成品入库、需求单累计口径，都必须把这张单一起回归。
