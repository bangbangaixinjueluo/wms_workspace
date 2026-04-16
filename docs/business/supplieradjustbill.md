# 供应商应付账调整
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责供应商应付账调整单的创建、修改、审核、反审、作废、导入与保存并审核。模块自身维护 `wh_supplier_adjust_bill`、`wh_supplier_adjust_bill_detail`，但真正的业务落点在钱包台账：审核时按明细把金额与金重分别记入供应商账户对应的钱包；反审时再按相反方向冲回。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|------|------|
| `wh_supplier_adjust_bill` | 读写 | 供应商应付账调整单主表，对应 `SupplierAdjustBillDO` |
| `wh_supplier_adjust_bill_detail` | 读写 | 调整明细，逐条指定供应商、材质、结算类别、金额与金重 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 审核/反审时按明细逐条更新钱包余额、钱包日志和日汇总 |

## 模块依赖
- `CommonService`：生成单号、获取当前登录人
- `SupplierService` / `AccountService`：校验供应商与账户关系
- `BusinessAreaService` / `BusinessStaffService`：按供应商回填业务员、业务分区、业务经理
- `MaterialService`：金重调整时解析原料与钱包类型
- `SettlementService`：导入时解析结算方式
- `WalletService`：执行钱包余额、日志、日汇总变更
- `SupplierAdjustBillDetailService` / `SupplierAdjustBillDetailMapper`：维护明细
- `DeptApi`：导入时补默认部门
- `PermissionApi`：反审跨日权限校验

## 接口列表

### createSupplierAdjustBill
> `SupplierAdjustBillController.java#createSupplierAdjustBill()` -> `SupplierAdjustBillServiceImpl.java#createSupplierAdjustBill()` `L38 / L104`

- 入口类型：HTTP 接口
- 用途：创建供应商应付账调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 业务逻辑：
  1. 校验明细非空。
  2. 按 `adjustDate` 生成单号。
  3. 写入主表，并保留原状态。
  4. `doInsertDetails()` 为每条明细回填 `billId/billNo`、业务员、业务分区、业务经理，并统一格式化 `amount/goldWeight`。
  5. 批量写入明细表。

### updateSupplierAdjustBill
> `SupplierAdjustBillController.java#updateSupplierAdjustBill()` -> `SupplierAdjustBillServiceImpl.java#updateSupplierAdjustBill()` `L47 / L146`

- 入口类型：HTTP 接口
- 用途：修改供应商应付账调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：
  - 单据必须存在。
  - 仅允许 `INIT(0)` 或 `INFIRM(3)` 修改。
- 业务逻辑：
  1. 更新主表并保留原状态。
  2. 删除旧明细。
  3. 按新请求重建明细。

### confirm
> `SupplierAdjustBillController.java#confirm()` -> `SupplierAdjustBillServiceImpl.java#confirm()` `L105 / L254`

- 入口类型：HTTP 接口
- 用途：审核供应商应付账调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 状态变更：
  - `wh_supplier_adjust_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedMan/approvedTime`
- 业务逻辑：
  1. 校验单据存在、明细非空。
  2. 遍历每条明细，分别处理金额与金重：
     - `amount != 0`：固定走现金钱包，`balanceDiff = amount`，`accountingType = DEPOSIT`
     - `goldWeight != 0`：按 `materialId + walletTypeNo + settleClass` 走材质钱包，`balanceDiff = goldWeight`，`accountingType = DEPOSIT`
  3. 所有钱包变更统一调用 `walletService.createWalletByAccountNew(...)`，由钱包服务同时更新余额、日汇总和日志。
  4. 最后把单头推进到已审。
- 关键结论：
  - 供应商应付账调整不动银行卡，只改钱包账。
  - 一条明细可以同时改两套钱包：金额改现金钱包，金重改材质钱包。

### unConfirm
> `SupplierAdjustBillController.java#unConfirm()` -> `SupplierAdjustBillServiceImpl.java#unConfirm()` `L115 / L336`

- 入口类型：HTTP 接口
- 用途：反审供应商应付账调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：
  - 当前状态必须是 `CONFIRM(2)`。
  - 若 `adjustDate` 跨日，必须拥有 `SUPPLIER_ADJUST_BILL_CROSS_DAYS` 权限。
- 状态变更：
  - `wh_supplier_adjust_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedMan/approvedTime`
- 业务逻辑：
  1. 遍历明细，对金额与金重做反向冲回：
     - 金额：`balanceDiff = -amount`，`accountingType = DEBT`
     - 金重：`balanceDiff = -goldWeight`，`accountingType = DEBT`
  2. 仍统一走 `createWalletByAccountNew()` 完成真实冲回。
  3. 最后把单头改成已反审。

### discard
> `SupplierAdjustBillController.java#discard()` -> `SupplierAdjustBillServiceImpl.java#discard()` `L125 / L425`

- 入口类型：HTTP 接口
- 用途：作废供应商应付账调整单
- 前置条件：
  - 当前实现只禁止 `CONFIRM`，未见统一 `validDiscard()` 校验。
- 业务逻辑：
  1. 校验单据存在。
  2. 把 `bill_status -> DISCARD(4)`。

### saveAndConfirm
> `SupplierAdjustBillServiceImpl.java#saveAndConfirm()`

- 入口类型：服务内聚合入口
- 用途：保存后立即审核
- 业务逻辑：
  1. `id` 为空则创建，否则更新。
  2. 紧接着执行 `confirm(id)`。

### dealImport
> `SupplierAdjustBillController.java#importExcel()` -> `SupplierAdjustBillServiceImpl.java#dealImport()` `L150 / L447`

- 入口类型：HTTP 导入
- 用途：从 Excel 批量导入多张供应商应付账调整单
- 业务逻辑：
  1. 校验文件并读取 Excel。
  2. 过滤空单号、合计行、无日期行。
  3. 校验网点、供应商、结算方式、材质、业务员、结算类别等主数据存在。
  4. 按 Excel `billNo` 分组，但系统会重新生成内部单号。
  5. 逐组落主表，再批量落明细。

## 公共规则

### 供应商应付账调整是“钱包侧调整单”
1. 审核/反审都只动钱包，不动银行卡。
2. 钱包余额、钱包日志、钱包日汇总由 `createWalletByAccountNew()` 一次完成。

### 同一明细支持金额账和金重账并存
1. `amount` 不为 0 时，调整现金钱包。
2. `goldWeight` 不为 0 时，调整材质钱包。
3. 两者可以同时存在，同一条明细会触发两次钱包动账。

### 金额与金重的审核/反审口径一致
1. 审核：`balanceDiff > 0`，`accountingType = DEPOSIT`
2. 反审：`balanceDiff < 0`，`accountingType = DEBT`

### 单据状态沿用标准 BillStatus
1. `INIT(0)`：未审
2. `CONFIRM(2)`：已审
3. `INFIRM(3)`：已反审
4. `DISCARD(4)`：已作废

## 当前结论
- 供应商应付账调整本质上是“供应商钱包台账调整单”，不是收付款那种“银行卡 + 钱包”双链入口。
- 审核会减少供应商在同心的应付款挂账，反审再把这笔挂账挂回去；这与 `WalletAccountingTypeEnum` 的“存账/欠账”说明是一致的。
- 这个模块和客户应收账调整几乎是镜像链路，风险点也集中在“现金钱包 + 材质钱包 + 日汇总 + 日志”的一致性。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteSupplierAdjustBill` | 供应商应付账调整 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅用于草稿态，是否应补状态校验 |
| ⚠️ | `confirm` | 供应商应付账调整 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | `doInsertDetails` | 供应商应付账调整明细 | 明细 `goldWeight` 被保留到 2 位小数，而表字段定义是 `decimal(10,3)`，存在精度口径下沉风险 | 待确认 | 确认业务上是否应该保留 3 位小数 |
| ⚠️ | `unConfirm` | 供应商应付账调整 | 反审分支里仍抛 `CUSTOMER_ADJUST_BILL_NOT_EXISTS`、`CUSTOMER_NOT_EXISTS` 这类客户侧错误码，异常语义明显串模块 | 待确认 | 确认是否应替换成供应商模块专用错误码 |
