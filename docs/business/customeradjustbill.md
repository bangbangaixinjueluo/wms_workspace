# 客户应收账调整
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责客户应收账调整单的创建、修改、审核、反审、作废、导入与保存并审核。模块本身维护 `wh_customer_adjust_bill`、`wh_customer_adjust_bill_detail`，但真正业务影响落在钱包侧：审核时按明细把金额和金重分别记入客户账户对应钱包；反审时再按相反方向冲回。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| `wh_customer_adjust_bill` | 读写 | 客户应收账调整单主表，对应 `CustomerAdjustBillDO` |
| `wh_customer_adjust_bill_detail` | 读写 | 客户应收账调整明细，逐条指定客户、材质、结算类别、金额与金重 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 审核/反审时按明细逐条更新钱包余额、钱包日志和日汇总 |

## 模块依赖
- `CommonService`：生成单号、获取当前登录人。
- `CustomerService` / `AccountService`：校验客户与账户关系。
- `BusinessAreaService` / `BusinessStaffService`：按客户回填业务员、业务分区、业务经理。
- `MaterialService`：金重调整时解析原料与 `materialNo/walletTypeNo`。
- `SettlementService`：导入时解析结算方式。
- `WalletService`：真正执行钱包余额、日汇总、日志变更。
- `CustomerAdjustBillDetailService` / `CustomerAdjustBillDetailMapper`：维护明细。
- `DeptApi`：导入时补默认部门。
- `PermissionApi`：反审跨日权限校验。

## 接口列表

### createCustomerAdjustBill
> `CustomerAdjustBillController.java#createCustomerAdjustBill()` -> `CustomerAdjustBillServiceImpl.java#createCustomerAdjustBill()` `L41 / L110`

- **入口类型**：HTTP 接口
- **用途**：创建客户应收账调整单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **业务逻辑**：
  1. 按 `adjustDate` 生成调整单号。
  2. 写入主表，并把 `rebate` 保留两位小数。
  3. 校验明细非空。
  4. `doSaveDetails()` 为每条明细回填 `billId/billNo`、业务员、业务分区、业务经理，并统一格式化 `amount/goldWeight`。
  5. 批量写入明细表。

### updateCustomerAdjustBill
> `CustomerAdjustBillController.java#updateCustomerAdjustBill()` -> `CustomerAdjustBillServiceImpl.java#updateCustomerAdjustBill()` `L49 / L160`

- **入口类型**：HTTP 接口
- **用途**：修改客户应收账调整单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 单据必须存在。
  - 仅允许 `INIT(0)` 或 `INFIRM(3)` 修改。
- **业务逻辑**：
  1. 更新主表并保留原状态。
  2. 重新格式化 `rebate`。
  3. 删除旧明细。
  4. 按新请求重新写明细。

### confirm
> `CustomerAdjustBillController.java#confirm()` -> `CustomerAdjustBillServiceImpl.java#confirm()` `L116 / L286`

- **入口类型**：HTTP 接口
- **用途**：审核客户应收账调整单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **状态变更**：
  - `wh_customer_adjust_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedMan/approvedTime`
- **业务逻辑**：
  1. 校验单据存在、明细非空。
  2. 遍历每条明细，分别处理金额与金重：
     - 金额不为 0：固定走现金钱包，`balanceDiff = amount`，`accountingType = DEPOSIT`
     - 金重不为 0：按 `materialId + walletTypeNo + settleClass` 走材质钱包，`balanceDiff = goldWeight`，`accountingType = DEPOSIT`
  3. 所有钱包变更统一调用 `walletService.createWalletByAccountNew(...)`，由钱包服务同时完成余额、日汇总、日志同步。
  4. 最后更新单头为已审。
- **关键结论**：
  - 客户应收账调整不碰银行卡，只改钱包账。
  - 一条明细可以同时改两套钱包：金额改现金钱包，金重改材质钱包。

### unConfirm
> `CustomerAdjustBillController.java#unConfirm()` -> `CustomerAdjustBillServiceImpl.java#unConfirm()` `L140 / L378`

- **入口类型**：HTTP 接口
- **用途**：反审客户应收账调整单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 当前状态必须是 `CONFIRM(2)`。
  - 若 `adjustDate` 跨日，必须拥有 `CUSTOMER_ADJUST_BILL_CROSS_DAYS` 权限。
- **状态变更**：
  - `wh_customer_adjust_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedMan/approvedTime`
- **业务逻辑**：
  1. 遍历明细，分别对金额与金重做反向冲回：
     - 金额：`balanceDiff = -amount`，`accountingType = DEBT`
     - 金重：`balanceDiff = -goldWeight`，`accountingType = DEBT`
  2. 仍统一走 `createWalletByAccountNew()`，由钱包服务执行真实冲回。
  3. 单头推进到 `INFIRM(3)`。

### discard
> `CustomerAdjustBillController.java#discard()` -> `CustomerAdjustBillServiceImpl.java#discard()` `L148 / L469`

- **入口类型**：HTTP 接口
- **用途**：作废客户应收账调整单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 必须满足 `BillStatusEnum.validDiscard()`，即只能作废 `INIT` 或 `INFIRM`。
- **业务逻辑**：
  1. 校验单据存在。
  2. 把 `bill_status -> DISCARD(4)`。

### saveAndConfirm
> `CustomerAdjustBillController.java#saveAndConfirm()` -> `CustomerAdjustBillServiceImpl.java#saveAndConfirm()` `L126 / L672`

- **入口类型**：HTTP 接口
- **用途**：保存并立即审核
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **业务逻辑**：
  1. `id` 为空则创建，否则更新。
  2. 紧接着执行 `confirm(id)`。

### dealImport
> `CustomerAdjustBillController.java#importExcel()` -> `CustomerAdjustBillServiceImpl.java#dealImport()` `L166 / L485`

- **入口类型**：HTTP 导入
- **用途**：从 Excel 批量导入多张客户应收账调整单
- **业务逻辑**：
  1. 校验文件并读取 Excel。
  2. 过滤空单号、合计行、无日期行。
  3. 校验网点、客户、结算方式、材质、业务员、结算类别等主数据存在。
  4. 按 Excel `billNo` 分组，但系统会重新生成内部单号。
  5. 逐组落主表，再批量落明细。

## 公共规则

### 客户应收账调整是“钱包侧调整单”
1. 审核/反审都只动钱包，不动银行卡。
2. 钱包余额、钱包日志、钱包日汇总由 `createWalletByAccountNew()` 一次完成。

### 同一明细支持金额账和金重账并存
1. `amount` 不为 0 时，调整现金钱包。
2. `goldWeight` 不为 0 时，调整材质钱包。
3. 两者可以同时存在，同一条明细会触发两次钱包动账。

### 金额与金重的审核/反审口径相同
1. 审核：`balanceDiff > 0`，`accountingType = DEPOSIT`
2. 反审：`balanceDiff < 0`，`accountingType = DEBT`

### 单据状态沿用标准 BillStatus
1. `INIT(0)`：未审
2. `CONFIRM(2)`：已审
3. `INFIRM(3)`：已反审
4. `DISCARD(4)`：已作废

## 当前结论
- 客户应收账调整本质上是“客户钱包台账调整单”，不是收付款那种银行 + 钱包双链动作。
- 审核会减少客户在同心的应收欠款，反审再把欠款挂回去；这一点和 `WalletAccountingTypeEnum` 的“存账/欠账”说明是一致的。
- 该模块对后续改造的风险点不在银行卡，而在“金额钱包 + 材质钱包 + 日汇总 + 日志”四层同步一致性。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteCustomerAdjustBill` | 客户应收账调整 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅用于草稿态，是否应补状态校验 |
| ⚠️ | `confirm` | 客户应收账调整 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | `doSaveDetails` | 客户应收账调整明细 | 明细 `goldWeight` 被保留到 2 位小数，而表字段定义是 `decimal(10,3)`，存在精度口径下沉风险 | 待确认 | 确认业务上是否应该保留 3 位小数 |
