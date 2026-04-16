# 收款单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责收款单的创建、修改、审核、反审、作废、导入、分账与取消分账。模块本身维护 `wh_receivable_bill`、`wh_receivable_bill_detail`、`wh_receivable_divide_bill`，但核心业务影响在资金侧：审核时同时回写网点银行卡余额与账户钱包余额，反审时双向回滚；分账时会先冲回原账户钱包，再按分账明细把金额重新分配到多个账户。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| `wh_receivable_bill` | 读写 | 收款单主表，对应 `ReceivableBillDO` |
| `wh_receivable_bill_detail` | 读写 | 收款单明细表，对应多笔收款渠道/银行明细 |
| `wh_receivable_divide_bill` | 读写 | 已审核收款单的分账结果 |
| `wh_shop_bank` / `wh_shop_bank_log` | 读写 | 审核/反审时更新银行余额与日志 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 审核/反审/分账/取消分账时更新账户钱包余额、日志与日结 |

## 模块依赖
- `CommonService`：生成单号、读取当前登录人。
- `CustomerService` / `SupplierService` / `BusinessAreaService` / `BusinessStaffService`：按业务类型回填客户、业务员、业务分区和经理。
- `ReceivableBillDetailService`：批量维护收款明细。
- `ReceivableDivideBillService`：分账与取消分账时维护 `wh_receivable_divide_bill`。
- `ShopBankService` / `ShopBankLogService`：更新银行卡余额、日汇总和银行日志。
- `WalletService` / `WalletLogService` / `WalletDailyService`：更新钱包余额、钱包日志和钱包日汇总。
- `PermissionApi`：反审跨日时做权限校验。

## 接口列表

### createReceivableBill
> `ReceivableBillController.java#createReceivableBill()` → `ReceivableBillServiceImpl.java#createReceivableBill()` `L67 / L126`

- **入口类型**：HTTP 接口
- **用途**：创建收款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **业务逻辑**：
  1. 调 `commonService.genBillNo(BillTypeEnum.FINANCE_RECEIPT.getPrefix())` 生成收款单号。
  2. 通过 `checkAndSetCustomer()` 按 `bizType` 回填客户/供应商、业务员、业务分区和业务经理。
  3. 校验明细非空。
  4. 通过 `calcAndCheckSubmitValues()` 按明细金额重算 `totalAmount`，以后端计算值为准。
  5. 写入主表后批量插入明细，并回填 `billId/billNo`。
- **关键结论**：
  - 表头 `totalAmount` 不是直接相信前端，而是按明细求和后回写。
  - 创建阶段不改资金余额，只有审核或分账相关动作才会改动资金侧数据。

### updateReceivableBill
> `ReceivableBillController.java#updateReceivableBill()` → `ReceivableBillServiceImpl.java#updateReceivableBill()` `L108 / L155`

- **入口类型**：HTTP 接口
- **用途**：修改收款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 单据必须存在。
  - 代码只允许 `billStatus = INIT(0)` 时修改，判断条件是 `ReceivableDivideEnum.INIT.getKey().equals(billStatus)`。
- **业务逻辑**：
  1. 重新回填客户/供应商侧业务属性。
  2. 重新按明细重算 `totalAmount`。
  3. 更新主表。
  4. 删除原明细并按请求全量重建。

### confirm
> `ReceivableBillController.java#confirm()` → `ReceivableBillServiceImpl.java#confirm()` `L209 / L242`

- **入口类型**：HTTP 接口
- **用途**：审核收款单，并正式入账。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **状态变更**：
  - `wh_receivable_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedTime/approvedMan`
- **业务逻辑**：
  1. 更新收款单头为已审。
  2. 调 `doShopBankLog(receivableBill, CONFIRM)`：
     - 按每条明细 `bankCode` 找到对应网点银行卡。
     - `balanceDiff = 明细 amount`
     - 更新 `wh_shop_bank.balance`
     - 更新 `shopBankService.refreshDaily(...)`
     - 写 `wh_shop_bank_log`
  3. 调 `setWalletAndLog(receivableBill, accountId, totalAmount)`：
     - 查或建现金钱包。
     - `diffBalance = -totalAmount`，把钱包余额向负方向推进。
     - 写 `wh_wallet_log`
     - 写 `walletDailyService.refresh(...)`
- **关键结论**：
  - 审核收款单会同时影响“银行余额”和“钱包余额”两条资金链。
  - 钱包侧使用 `diffBalance = totalAmount.negate()`，说明收款单在钱包账务上按“冲减负债/预收占用”口径处理，而不是简单把余额加正。

### unConfirm
> `ReceivableBillController.java#unConfirm()` → `ReceivableBillServiceImpl.java#unConfirm()` `L217 / L315`

- **入口类型**：HTTP 接口
- **用途**：反审已审核收款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 当前状态必须是 `CONFIRM`。
  - 若 `payDate` 已跨日，则必须具备权限 `RECEIVABLE_BILL_CROSS_DAYS`。
- **业务逻辑**：
  1. 把单据状态回退到 `INIT(0)`，并清空审核人/审核时间。
  2. 调 `delWalletAndLog(receivableBill, accountId, totalAmount)` 反向回滚钱包余额与钱包日汇总。
  3. 删除该单据号对应的钱包日志 `walletLogService.deleteByBillNo(billNo)`。
  4. 调 `doShopBankLog(receivableBill, INIT)`，由于内部 `balanceDiff = balance.negate()`，会反向扣回银行余额与银行日汇总，并写一条银行日志。
- **关键结论**：
  - 反审不是只改单头，而是完整回滚审核时的两条资金链。
  - 当前实现把状态直接回退到 `INIT`，没有独立的 `INFIRM` 状态流。

### discard
> `ReceivableBillController.java#discard()` → `ReceivableBillServiceImpl.java#discard()` `L225 / L355`

- **入口类型**：HTTP 接口
- **用途**：作废收款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 仅禁止作废 `CONFIRM` 状态。
- **业务逻辑**：
  1. 若单据不存在，直接报错。
  2. 若当前状态是 `CONFIRM`，拒绝作废。
  3. 否则直接把 `bill_status -> DISCARD(4)`。

### divideReceivableBill
> `ReceivableBillController.java#divideReceivableBill()` → `ReceivableBillServiceImpl.java#divideReceivableBill()` `L75 / L414`

- **入口类型**：HTTP 接口
- **用途**：对已审核的收款单做分账。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 只能对 `bill_status = CONFIRM(2)` 的收款单分账。
  - 分账金额合计必须等于收款金额。
- **业务逻辑**：
  1. 先调用 `delWalletAndLog(receivableBillDO, 原 accountId, totalAmount)`，把原始账户上的钱包影响冲回。
  2. 删除该单据号下原有钱包日志。
  3. 遍历分账明细，对每个 `accountId/amount` 调 `setWalletAndLog()`，把金额重新落到分账后的账户钱包。
  4. 同步写 `wh_receivable_divide_bill`。
  5. 把收款单头状态改为 `DIVIDE(3)`，并刷新审核人/审核时间。
- **关键结论**：
  - 分账只改“钱包归属”，不改银行余额；银行余额在审核时已经落账，分账阶段不重做银行侧。

### unDivideReceivableBill
> `ReceivableBillController.java#unDivideReceivableBill()` → `ReceivableBillServiceImpl.java#unDivideReceivableBill()` `L83 / L454`

- **入口类型**：HTTP 接口
- **用途**：取消已分账收款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 只能对 `bill_status = DIVIDE(3)` 的收款单取消分账。
  - 分账金额合计必须等于收款金额。
- **业务逻辑**：
  1. 遍历分账明细，对每个分账账户执行 `delWalletAndLog()`，把对应钱包影响冲回。
  2. 删除该单据号的钱包日志。
  3. 删除分账记录 `wh_receivable_divide_bill`。
  4. 再把整笔金额通过 `setWalletAndLog()` 重新挂回原始账户。
  5. 把收款单头状态回退到 `CONFIRM(2)`，并刷新审核人/审核时间。

### importExcel
> `ReceivableBillController.java#importExcel()` → `ReceivableBillServiceImpl.java#importExcel()` `L249 / L498`

- **入口类型**：HTTP 导入
- **用途**：批量导入收款单。
- **业务逻辑**：
  1. 校验日期、单据号、客户名、金额非空。
  2. 以导入文件中的 `billNo` 分组。
  3. 通过业务员、业务分区、客户/供应商名称、网点名称补足头信息。
  4. 自动重新生成系统 `billNo`，不是沿用导入单号。
  5. 默认 `shopId = 1010L`，并按行号写入明细。

## 公共规则

### 收款单主状态不是普通“未审/已反审”模型
1. 实际状态来自 `ReceivableDivideEnum`：
   - `INIT(0)`：初始
   - `CONFIRM(2)`：已审
   - `DIVIDE(3)`：已分账
   - `DISCARD(4)`：已作废
2. 反审不是进入 `INFIRM`，而是直接回到 `INIT`。
3. 分账是额外业务状态，不是单纯的附属动作。

### 审核与分账的资金影响范围不同
1. 审核/反审会同时影响：
   - 网点银行卡余额、银行日汇总、银行日志
   - 账户钱包余额、钱包日汇总、钱包日志
2. 分账/取消分账只重分配钱包归属，不重新影响银行侧资金。

### 收款单金额以后端明细重算为准
1. `calcAndCheckSubmitValues()` 会按所有明细 `amount` 求和。
2. 如果和前端提交的 `totalAmount` 不一致，只打告警日志，但最终仍以后端计算值回写到单据表头。

## 当前结论
- 收款单模块不是单纯的财务单据头表，它实际上是“银行余额 + 钱包余额”双账本的驱动入口。
- 审核/反审与分账/取消分账是两套不同的资金变更链：前者动银行和钱包，后者只动钱包归属。
- 收款单头状态除了 `INIT/CONFIRM/DISCARD`，还存在显式的 `DIVIDE` 分账态，后续做应收或账户改造时必须一起考虑。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteReceivableBill` | 收款单 / 收款单明细 | 仅按主键物理删除主表，未看到状态校验，也未同步删除明细和资金侧痕迹 | 待确认 | 确认该接口是否仍对外使用，是否应限制为草稿态并补齐级联清理 |
| ⚠️ | `discard` | 收款单 / 分账 | 只禁止 `CONFIRM`，未禁止 `DIVIDE`，已分账单据理论上可直接作废且不回滚钱包分配 | 待确认 | 确认作废前是否应显式拦截 `DIVIDE` 或强制先取消分账 |
| ⚠️ | `updateReceivableBill` | 收款单 | 提示文案写“未审和已反审可修改”，但代码只允许 `INIT`，且本模块没有独立 `INFIRM` 状态 | 待确认 | 确认文案是否过时，或是否缺少独立反审状态设计 |
