# 成品调拨单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责成品调拨单的创建、修改、审核、接收、驳回、退回接收、退回拒签、反审与作废，并在关键状态切换时回写源单据的调拨信息。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_goods_transfer_bill | 读写 | 成品调拨单主表，对应 `GoodsTransferBillDO` |
| wh_goods_transfer_bill_detail | 读写 | 成品调拨单明细表，对应 `GoodsTransferBillDetailDO` |

## 接口列表

### createGoodsTransferBill
> `GoodsTransferBillController.java#createGoodsTransferBill()` `→` `GoodsTransferBillServiceImpl.java#createGoodsTransferBill()` `→ L75 / L182`

- **用途**：创建成品调拨单及明细。
- **业务逻辑**：
  1. 生成单号。
  2. 要求 `detailList` 非空。
  3. 规整每行的 `goldWeight/num/weight/grossWeight`。
  4. 若来源是维修单明细，则要求 `originBillDetailId` 非空且来源流转仓与单头仓一致。
  5. 重算总件数、总金重、总重。
  6. 做成品库存校验。
  7. 写入主表和明细表。

### updateGoodsTransferBill
> `GoodsTransferBillController.java#updateGoodsTransferBill()` `→` `GoodsTransferBillServiceImpl.java#updateGoodsTransferBill()` `→ L84 / L323`

- **用途**：更新成品调拨单。
- **前置条件**：仅允许 `INIT/INFIRM`。
- **业务逻辑**：
  1. 校验调拨单存在且状态允许修改。
  2. 重做明细规整和维修来源校验。
  3. 重算总字段并再次做成品库存校验。
  4. 更新主表。
  5. 删除旧明细并重建新明细。

### confirm
> `GoodsTransferBillController.java#confirm()` `→` `GoodsTransferBillServiceImpl.java#comfirm()` / `doConfirm()` `→ L191 / L576`

- **用途**：审核成品调拨单。
- **状态变更**：
  - 普通仓调入：`bill_status -> CONFIRM`
  - 源单据：`transferStatus -> BE_TRANSFERRED`
  - 源单据回写：`transferBillId/transferBillNo/transferType`
- **业务逻辑**：
  1. 校验库存与重复调拨。
  2. 若目的仓不是展销仓，则直接审核通过。
  3. 若目的仓是展销仓，则发起 BPM，进入 `CONFIRMING`。
  4. 审核通过时调用 `doOriginBillStatus()` 回写源单据。

### received
> `GoodsTransferBillController.java#received()` `→` `GoodsTransferBillServiceImpl.java#received()` `→ L208 / L733`

- **用途**：接收确认成品调拨单。
- **前置条件**：当前状态必须是 `CONFIRM`。
- **状态变更**：
  - `bill_status`：`CONFIRM -> RECEIVED`
  - 源单据：调拨状态进入“已签收”类终态
- **业务逻辑**：
  1. 更新接收人、接收时间。
  2. 读取明细并校验不为空。
  3. 调出仓扣减库存，调入仓增加库存。

### rejected
> `GoodsTransferBillController.java#rejected()` `→` `GoodsTransferBillServiceImpl.java#rejected()` `→ L225 / L1050`

- **用途**：拒签成品调拨单。
- **状态变更**：
  - `bill_status`：`* -> REJECTED`
  - 源单据：`transferStatus -> REJECTED_SIGNED`

### backReceived
> `GoodsTransferBillController.java#backReceived()` `→` `GoodsTransferBillServiceImpl.java#backReceived()` `→ L255 / L1091`

- **用途**：对“已驳回”或“退回中”的成品调拨单执行退回接收。
- **前置条件**：当前状态必须满足 `canReturnReceive()`，即 `REJECTED/RETURNING`。
- **状态变更**：
  - `bill_status`：`REJECTED/RETURNING -> BACK_RECEIVED`
  - 源单据：`transferStatus -> RETURNED_SIGNED`
- **业务逻辑**：
  1. 更新调拨单状态为 `BACK_RECEIVED`。
  2. 若原状态为 `RETURNING`，执行调出仓/调入仓库存回滚，并恢复源单当前仓。
  3. 若原状态为 `REJECTED`，不再额外调整库存。
  4. 回写字印和维修关联数据。

### backRejected
> `GoodsTransferBillController.java#backRejected()` `→` `GoodsTransferBillServiceImpl.java#backRejected()` `→ L264 / L1228`

- **用途**：对“退回中”的成品调拨单执行退回拒签。
- **前置条件**：当前状态必须是 `RETURNING`。
- **状态变更**：
  - `bill_status`：`RETURNING -> BACK_REJECTED`
  - 源单据：`transferStatus -> SIGNED`

### infirm
> `GoodsTransferBillController.java#infirm()` `→` `GoodsTransferBillServiceImpl.java#infirm()` `→ L217 / L978`

- **用途**：反审成品调拨单。
- **前置条件**：状态必须满足 `canInfirm()`，即 `CONFIRM/BACK_RECEIVED`。
- **状态变更**：
  - `bill_status`：`CONFIRM/BACK_RECEIVED -> INFIRM`
  - 源单据：若无上层调拨单则回到 `INIT`，否则回到 `SIGNED`

### discard
> `GoodsTransferBillController.java#discard()` `→` `GoodsTransferBillServiceImpl.java#discard()` `→ L199 / L671`

- **用途**：作废未审或已反审的成品调拨单。
- **前置条件**：仅允许 `INIT/INFIRM`。
- **状态变更**：
  - `bill_status`：`INIT/INFIRM -> DISCARD`
  - 源单据：清空 `transferBillId/transferBillNo`

### saveAndConfirm
> `GoodsTransferBillController.java#saveAndConfirm()` `→` `GoodsTransferBillBusiness.java#saveAndConfirm()` `→ L274 / L205`

- **用途**：一键保存并审核成品调拨单。
- **业务逻辑**：
  1. 有 `id` 时先更新。
  2. 无 `id` 时先创建。
  3. 然后调用 `comfirm(id)`。

## 源单据回写规则
> `GoodsTransferBillServiceImpl.java#doOriginBillStatus()` `→ L614-L640`

- 若源单据是产品包：
  - 写 `pack.transferType = transferBill.transferType`
  - 写 `pack.transferBillId = transferBill.id`
  - 写 `pack.transferBillNo = transferBill.billNo`
  - 写 `pack.transferStatus = {传入状态}`
- 若源单据是结算单：
  - 同步写 `saleBill.transferType/transferBillId/transferBillNo/transferStatus`

## 当前结论
- 成品调拨单和单件调拨单共用同一套 `TransferBillStatusEnum` 状态机。
- 成品调拨单在审核、退回接收、退回拒签时，同样会回写产品包的调拨字段，因此可以直接回填 `pack.genConfirmTransfer()` 和 `pack.backRejected()`。
