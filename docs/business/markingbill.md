# 字印单

## 模块定位
- 对应表：`wh_marking_bill`、`wh_marking_detail`
- 入口：`MarkingBillController`、`MarkingBillBusiness`、`MarkingBillServiceImpl`
- 这不是普通加工记录单，而是“产品包/销售结算字印来源池 + 调拨承接宿主 + 单件锁定与释放链”的执行单。

## 主数据职责
- 单头承载：
  - 来源单 `originType/originBillId/originBillNo`
  - 调拨挂接 `transferBillId/transferBillNo`
  - 客户字印 `markingNo`
  - 汇总值 `totalNum/totalGoldWeight/totalWeight/totalGrossWeight`
  - 状态 `billStatus`
- 单身 `wh_marking_detail` 承载真正待字印的 EPC/条码、SKU、规格、重量与 `printStatus`。

## 主状态流
- 当前从代码能确认的主状态包括：
  - `INIT`
  - `CONFIRM`
  - `INFIRM`
  - `MARKING`
  - `MARKING_FINISHED`
  - `BACKING`
  - `DISCARD`
- 这条链不只是审核态，还包含“字印中/字印完成/调回中”这类执行态。

## 建单与改单
- `create(...)`
  - 生成字印单号。
  - 校验同一 `transferBillId` 下不存在未结束字印单。
  - 回填客户业务归属字段。
  - 计算合计件数、金重、重量、毛重。
  - 保存单头和明细。
  - 执行 `billAssociation(...)`，把字印单号/ID 回写到来源产品包或来源销售结算单，同时回写调拨单。
- `update(...)`
  - 只允许 `INIT/INFIRM`。
  - 重算合计值。
  - 先删旧明细，再重插新明细。

## 审核与反审
- `confirm(id)`
  - 校验状态可审核、明细非空。
  - 若是 `SG_MARKING` 单件字印：
    - 校验每个 EPC 存在且仍在当前仓库。
    - 对每个 EPC 执行 `itemService.customerLock(...)`。
    - 批量写“字印单审核锁定”单件日志。
  - 最终把单头推进到 `CONFIRM`，写审核人和审核时间。
- `infirm(id)`
  - 只允许从 `CONFIRM` 反审。
  - 若是单件字印，会对整单 EPC 执行 `unlockByEpcList(...)`。
  - 单头回到 `INFIRM`，并清空审核人/审核时间。

## 执行态流转
- `marking(id)`
  - 只允许 `CONFIRM -> MARKING`。
- `marked(id)`
  - 只允许 `MARKING -> MARKING_FINISHED`。
  - 若是单件字印，会释放整单 EPC 锁。
  - 同时把全部明细 `printStatus` 改成“已字印”。
  - 写入 `markingFinishTime`。
- `obsolete(id)`
  - 按作废规则把单头改成 `DISCARD`。
  - 明细侧通过 `effective` 做失效标记。
  - 再执行 `billDisassociate(...)`，解除来源单和调拨单上的字印挂接。
- `back(id)`
  - 只允许满足 `canBack(...)` 的状态。
  - 先把字印单头推到 `BACKING`。
  - 再按业务类型把挂接调拨单推进“退回中”。

## 来源与回写
- `billAssociation(...)`
  - 来源是产品包时，回写 `pack.markingBillId/markingBillNo`
  - 来源是销售结算单时，回写 `saleBill.markingBillId/markingBillNo`
  - 同时回写调拨单 `markingBillId/markingBillNo`
- `billDisassociate(...)`
  - 作废时会把这些挂接反向清空。
- `genForTransfer(...)`
  - 可以根据调拨单直接生成字印单草稿视图。
  - 还会尝试从原始产品包或销售结算单回填 `markingNo`。

## 调拨承接链
- 这条链不是字印单自己搬货，而是承接“调拨到字印仓”的转运结果。
- `getTransferPage(...)`
  - 固定按 `destWareId = wh.marking.wareId` 查字印仓目的调拨。
- `receive/rejected/returning`
  - 根据 `bizType` 分流到单件调拨 `transferBillService` 或成品调拨 `goodsTransferBillService`。
- `packReceive/packReject/packReturning`
  - 是对产品包来源的专门入口。
- `saleReceive/saleReject/saleReturning`
  - 是对销售结算来源的专门入口。

## 关联模块
- `pack`
  - 字印单可直接挂在产品包上。
- `salebill`
  - 字印单也可直接挂在销售结算单上。
- `transferbill` / `goodstransferbill`
  - 真正承担字印仓往返转运。
- `customermarking`
  - 通过 `markingNo` 反查字印内容。
- `item` / `itemlog`
  - 单件字印审核和完工会锁定/释放 EPC，并记录日志。

## 关键结论
- 字印单不是简单的打印任务表，而是字印执行域宿主。
- 它把来源业务单、调拨链、字印执行态、单件锁定链串成了一条完整业务流。
- 对单件字印来说，审核锁定 EPC，完工再释放 EPC，是这条链最关键的执行语义。

## 风险与疑点
- `⚠️` `deleteMarkingBill(...)` 当前直接物理删单头和明细，未见状态限制，也未看到同步清理来源单/调拨单挂接。
- `⚠️` `marked(...)` 直接把整单明细 `printStatus` 全量改成已字印，没有逐行完成校验。
- `⚠️` `infirm(...)` 和 `marked(...)` 都直接 `unlockByEpcList(...)`，未见对其他占用链的额外校验。
- `⚠️` `back(id)` 会先把单头改成 `BACKING`，再调底层调拨“退回中”；若后一步失败，需要确认事务外是否还有补偿口径。
