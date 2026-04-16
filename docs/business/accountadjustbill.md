# 调账单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责账户间调账单的创建、修改、审核、反审、作废。模块自身维护 `wh_account_adjust_bill`、`wh_account_adjust_bill_detail`，真正业务效果落在钱包台账：审核时把金额/金重从调出账户冲减、向调入账户补入；反审时再整笔反向回滚。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|------|------|
| `wh_account_adjust_bill` | 读写 | 调账单主表，对应 `AccountAdjustBillDO` |
| `wh_account_adjust_bill_detail` | 读写 | 调账明细，承载逐行金额/金重 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 审核/反审时按调出账户、调入账户成对写入钱包余额、日志与日汇总 |

## 模块依赖
- `CommonService`：生成单号、获取当前登录人
- `AccountService` / `AccountMapper`：校验调出/调入账户
- `WalletService`：执行真钱包动账
- `MaterialService`：解析金重链使用的材质、钱包类型和材质编码
- `CustomerService` / `BusinessAreaService`：当账户类型是客户时回填业务员、业务分区、业务经理
- `ShopService`：页面展示与回填网点信息
- `PermissionApi`：跨日反审权限校验
- `AccountAdjustBillDetailService`：维护明细

## 接口列表

### createAccountAdjustBill
> `AccountAdjustBillController.java#createAccountAdjustBill()` -> `AccountAdjustBillServiceImpl.java#createAccountAdjustBill()` `L38 / L97`

- 入口类型：HTTP 接口
- 用途：创建调账单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 业务逻辑：
  1. 生成调账单号。
  2. 写入主表，并在客户账户场景下回填调出/调入侧业务员、业务分区、业务经理。
  3. 校验明细非空。
  4. 按明细重算 `totalAmount/totalGoldWeight`，覆盖前端提交汇总值。
  5. 批量写入 `wh_account_adjust_bill_detail`。

### updateAccountAdjustBill
> `AccountAdjustBillController.java#updateAccountAdjustBill()` -> `AccountAdjustBillServiceImpl.java#updateAccountAdjustBill()` `L46 / L169`

- 入口类型：HTTP 接口
- 用途：修改调账单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：
  - 单据必须存在。
  - 仅允许 `INIT(0)` 或 `INFIRM(3)` 修改。
- 业务逻辑：
  1. 重新按明细计算合计金额、合计金重。
  2. 更新主表。
  3. 先删旧明细，再重建新明细。

### confirm
> `AccountAdjustBillController.java#confirm()` -> `AccountAdjustBillServiceImpl.java#confirm()` `L118 / L386`

- 入口类型：HTTP 接口
- 用途：审核调账单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 状态变更：
  - `wh_account_adjust_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedMan/approvedTime`
- 业务逻辑：
  1. 校验单据、调出账户、调入账户存在。
  2. 若金额和金重同时为空则拒绝。
  3. 金额链：
     - 调出账户：`balanceDiff = -totalAmount`
     - 调入账户：`balanceDiff = totalAmount`
     - 固定走现金钱包：`walletTypeNo = CASH`、`settleClass = SETTLE_CASH`
  4. 金重链：
     - 按 `materialId` 找材质，取 `materialNo/walletTypeNo`
     - 调出账户：`balanceDiff = -totalGoldWeight`
     - 调入账户：`balanceDiff = totalGoldWeight`
     - 材质钱包口径由 `settleClass` 控制
  5. 四笔动账统一走 `walletService.createWalletByAccountNew(...)`。
- 关键结论：
  - 调账单不是单边钱包调整，而是“调出 + 调入”成对过账。
  - 一张单可同时触发四笔钱包动作：金额两笔、金重两笔。

### unConfirm
> `AccountAdjustBillController.java#unConfirm()` -> `AccountAdjustBillServiceImpl.java#unConfirm()` `L127 / L508`

- 入口类型：HTTP 接口
- 用途：反审调账单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：
  - 当前状态必须是 `CONFIRM(2)`。
  - 若 `adjustDate` 跨日，必须拥有 `ACCOUNT_ADJUST_BILL_CROSS_DAYS` 权限。
- 状态变更：
  - `wh_account_adjust_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedMan/approvedTime`
- 业务逻辑：
  1. 金额链整体反向：
     - 调出账户：`balanceDiff = totalAmount`
     - 调入账户：`balanceDiff = -totalAmount`
  2. 金重链整体反向：
     - 调出账户：`balanceDiff = totalGoldWeight`
     - 调入账户：`balanceDiff = -totalGoldWeight`
  3. 仍统一走 `createWalletByAccountNew()` 完成钱包冲回。

### discard
> `AccountAdjustBillController.java#discard()` -> `AccountAdjustBillServiceImpl.java#discard()` `L136 / L629`

- 入口类型：HTTP 接口
- 用途：作废调账单
- 业务逻辑：
  1. 校验单据存在。
  2. 当前实现只禁止 `CONFIRM`，未见统一 `validDiscard()` 校验。
  3. 将 `bill_status -> DISCARD(4)`。

## 公共规则

### 调账单是“双账户对敲钱包链”
1. 审核不是单边加减，而是调出账户、调入账户同时落账。
2. 金额链和金重链互相独立，可以只调金额、只调金重，也可以同时调两者。
3. 所有实际余额、日志、日汇总更新都由 `createWalletByAccountNew()` 完成。

### 金额链固定走现金钱包
1. `walletTypeNo = CASH`
2. `materialNo = CASH.defaultMaterialNo`
3. `settleClass = SETTLE_CASH`

### 金重链按材质钱包过账
1. 必须存在 `materialId`
2. `walletTypeNo/materialNo` 来自材质档案
3. `settleClass` 直接沿用单头字段

### 单据状态沿用标准 BillStatus
1. `INIT(0)`：未审
2. `CONFIRM(2)`：已审
3. `INFIRM(3)`：已反审
4. `DISCARD(4)`：已作废

## 当前结论
- 调账单是财务侧“账户对账户转移”的钱包链，不走银行卡，也不是客户/供应商那种单边挂账调整。
- 它的核心不是明细逐条落账，而是把单头汇总值拆成“调出一笔、调入一笔”的镜像双分录；金额和金重各自再复制一遍。
- 如果后续改这个模块，最容易漏掉的不是单头状态，而是四笔钱包动作必须同时保持方向一致。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteAccountAdjustBill` | 调账单 / 调账明细 | 只删主表，没看到级联删除明细，也没见状态限制 | 待确认 | 确认删除接口是否只允许草稿态，是否应补明细级联清理 |
| ⚠️ | `confirm` | 调账单 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | `confirm/unConfirm/discard` | 调账单 | 单据不存在时仍抛 `CUSTOMER_ADJUST_BILL_NOT_EXISTS`，错误码明显串模块 | 待确认 | 确认是否应统一切回 `ACCOUNT_ADJUST_BILL_NOT_EXISTS` |
| ⚠️ | `createAccountAdjustBill/updateAccountAdjustBill` | 调账明细 | `goldWeight` 写入时保留 2 位小数，但表字段定义是 `decimal(10,3)`，存在精度下沉风险 | 待确认 | 确认业务上是否应该保留 3 位小数 |
