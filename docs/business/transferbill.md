# 调拨单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责单件调拨单的创建、修改、审核、接收、驳回、退回接收、退回拒签、反审与作废，并在关键状态切换时回写源单据的调拨状态。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_transfer_bill | 读写 | 调拨单主表，对应 `TransferBillDO` |
| wh_transfer_bill_detail | 读写 | 调拨单明细表，对应 `TransferBillDetailDO` |

## 接口列表

### createTransferBill
> `TransferBillController.java#createTransferBill()` `→` `TransferBillServiceImpl.java#createTransferBill()` `→ L57 / L213`

- **用途**：创建单件调拨单及其明细。
- **业务逻辑**：
  1. 生成调拨单号。
  2. 要求 `detailList` 非空。
  3. 以 EPC 为键检查近 7 天内同源仓是否已存在重复调拨明细。
  4. 汇总件数与金重，写入 `wh_transfer_bill`。
  5. 校验每个 EPC 存在，且来源网点、来源仓与单头一致。
  6. 写入 `wh_transfer_bill_detail`。

### updateTransferBill
> `TransferBillController.java#updateTransferBill()` `→` `TransferBillServiceImpl.java#updateTransferBill()` `→ L66 / L473`

- **用途**：修改单件调拨单。
- **前置条件**：仅允许 `INIT` 或 `INFIRM` 状态修改。
- **业务逻辑**：
  1. 校验明细非空。
  2. 校验调拨单存在且状态允许修改。
  3. 重做 EPC 重复检查。
  4. 重算总件数、总金重并更新主表。
  5. 删除不再保留的旧明细，重建本次明细。

### confirm
> `TransferBillController.java#confirm()` `→` `TransferBillServiceImpl.java#confirm()` `→ L93 / L1040`

- **用途**：审核单件调拨单。
- **前置条件**：
  - 调拨单状态必须满足 `TransferBillStatusEnum.canConfirm()`，即 `INIT/INFIRM`
  - 单件必须仍在调出仓
- **状态变更**：
  - 普通仓调入：`wh_transfer_bill.bill_status`：`INIT/INFIRM -> CONFIRM`
  - 源单据：`transferStatus -> BE_TRANSFERRED`
  - 源单据回写：`transferBillId/transferBillNo/transferType`
- **业务逻辑**：
  1. 校验跨日审核权限。
  2. 校验目的仓存在。
  3. 校验单件当前仓等于调出仓。
  4. 校验是否重复生成调拨单。
  5. 若目标仓不是展销仓，直接审核通过并把单件推进在途。
  6. 若目标仓是展销仓，则发起 BPM，单据进入 `CONFIRMING`。

### received
> `TransferBillController.java#received()` `→` `TransferBillServiceImpl.java#received()` `→ L111 / L706`

- **用途**：接收确认单件调拨单。
- **前置条件**：当前状态必须是 `CONFIRM`。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`CONFIRM -> RECEIVED`
  - 源单据：调拨状态进入“已签收”类终态
- **业务逻辑**：
  1. 更新接收人、接收时间。
  2. 读取明细并校验不为空。
  3. 调整单件仓位/库位及相关日志。

### rejected
> `TransferBillController.java#rejected()` `→` `TransferBillServiceImpl.java#rejected()` `→ L168 / L1331`

- **用途**：拒签调拨单。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`* -> REJECTED`
  - 源单据：`transferStatus -> REJECTED_SIGNED`

### backReceived
> `TransferBillController.java#backReceived()` `→` `TransferBillServiceImpl.java#backReceived()` `→ L194 / L1397`

- **用途**：对“已驳回”或“退回中”的单件调拨单执行退回接收。
- **前置条件**：`TransferBillStatusEnum.canReturnReceive()`，即当前状态必须是 `REJECTED` 或 `RETURNING`。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`REJECTED/RETURNING -> BACK_RECEIVED`
  - 源单据：`transferStatus -> RETURNED_SIGNED`
- **业务逻辑**：
  1. 更新调拨单状态为 `BACK_RECEIVED`。
  2. 若原状态为 `RETURNING`，执行单件退回、恢复原当前仓。
  3. 若原状态为 `REJECTED`，则把单件从在途/库内状态恢复。
  4. 回写客留存状态、字印单、维修单关联数据。

### backRejected
> `TransferBillController.java#backRejected()` `→` `TransferBillServiceImpl.java#backRejected()` `→ L203 / L1501`

- **用途**：对“退回中”的单件调拨单执行退回拒签。
- **前置条件**：当前状态必须是 `RETURNING`。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`RETURNING -> BACK_REJECTED`
  - 源单据：`transferStatus -> SIGNED`
- **业务逻辑**：
  1. 更新调拨单状态为 `BACK_REJECTED`。
  2. 把源单据调拨状态改回已签收态。
  3. 若调拨类型涉及字印仓，还会同步回写字印单状态。

### infirm
> `TransferBillController.java#infirm()` `→` `TransferBillServiceImpl.java#infirm()` `→ L103 / L1290`

- **用途**：反审调拨单。
- **前置条件**：状态必须满足 `TransferBillStatusEnum.canInfirm()`，即 `CONFIRM/BACK_RECEIVED`。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`CONFIRM/BACK_RECEIVED -> INFIRM`
  - 源单据：若无上层调拨单则回到 `INIT`，否则回到 `SIGNED`
- **业务逻辑**：
  1. 校验跨日反审权限。
  2. 恢复单件状态。
  3. 清空审核人、审核时间。
  4. 回退客留存调拨状态。

### discard
> `TransferBillController.java#discard()` `→` `TransferBillServiceImpl.java#discard()` `→ L120 / L645`

- **用途**：作废未审或已反审的调拨单。
- **前置条件**：仅允许 `INIT/INFIRM`。
- **状态变更**：
  - `wh_transfer_bill.bill_status`：`INIT/INFIRM -> DISCARD`
  - 源单据：清空 `transferBillId/transferBillNo`

### saveAndConfirm
> `TransferBillController.java#saveAndConfirm()` `→` `TransferBillBusiness.java#saveAndConfirm()` `→ L213 / L303`

- **用途**：一键保存并审核单件调拨单。
- **业务逻辑**：
  1. 有 `id` 时先更新。
  2. 无 `id` 时先创建。
  3. 然后调用 `confirm(id)`。

## 公共状态机
> `TransferBillStatusEnum.java` `→ L18-L27, L77-L106`

- 主状态至少包括：`INIT`、`CONFIRMING`、`CONFIRM`、`INFIRM`、`DISCARD`、`RECEIVED`、`REJECTED`、`RETURNING`、`BACK_RECEIVED`、`BACK_REJECTED`
- `canConfirm`：`INIT/INFIRM`
- `canInfirm`：`CONFIRM/BACK_RECEIVED`
- `canReceive`：`CONFIRM/BACK_REJECTED`
- `canReturnReceive`：`REJECTED/RETURNING`

## 当前结论
- 单件调拨单在审核、退回接收、退回拒签时，都会同步回写源单据的 `transferStatus`。
- 若源单据是产品包，则审核时会写入 `transferBillId/transferBillNo/transferType/transferStatus`；作废时会清空 `transferBillId/transferBillNo`。
