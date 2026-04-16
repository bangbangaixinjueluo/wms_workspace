# 付款单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责付款单的创建、修改、审核、反审、作废与导入。模块本身维护 `wh_payable_bill`、`wh_payable_bill_detail`，但核心业务影响落在资金侧：审核时同步扣减网点银行卡余额、增加账户钱包余额；反审时再按相反方向回滚这两条资金链。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| `wh_payable_bill` | 读写 | 付款单主表，对应 `PayableBillDO` |
| `wh_payable_bill_detail` | 读写 | 付款单明细，按银行账号拆分付款渠道 |
| `wh_shop_bank` / `wh_shop_bank_log` | 读写 | 审核/反审时更新银行卡余额、日汇总和银行日志 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 审核/反审时更新账户钱包余额、钱包日志和钱包日汇总 |

## 模块依赖
- `CommonService`：生成单号、读取当前登录人。
- `AccountService` / `CustomerService` / `SupplierService`：校验付款对象与储户账户关系。
- `BusinessAreaService` / `BusinessStaffService`：按业务类型回填业务员、业务分区、业务经理。
- `PayableBillDetailService`：批量维护付款单明细。
- `ShopBankService` / `ShopBankLogService`：更新银行卡余额、日汇总和银行日志。
- `WalletService` / `WalletLogService` / `WalletDailyService`：更新钱包余额、日志和日汇总。
- `PermissionApi`：反审跨日时做权限校验。

## 接口列表

### createPayableBill
> `PayableBillController.java#createPayableBill()` -> `PayableBillServiceImpl.java#createPayableBill()` `L43 / L119`

- **入口类型**：HTTP 接口
- **用途**：创建付款单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **业务逻辑**：
  1. 调用 `commonService.genBillNo(BillTypeEnum.FINANCE_PAY.getPrefix())` 生成付款单号。
  2. 通过 `setCustomerInfo()` 按 `bizType` 回填客户/供应商、业务员、业务分区和业务经理信息。
  3. 校验明细非空。
  4. 调用 `calcAndCheckSubmitValues()` 按明细金额重算 `totalAmount`，以后端计算值为准。
  5. 先写主表，再批量写入 `wh_payable_bill_detail`。
- **关键结论**：
  - 付款单表头金额不直接相信前端，而是由明细重算回写。
  - 创建阶段不改资金余额，真正落账发生在审核/反审。

### updatePayableBill
> `PayableBillController.java#updatePayableBill()` -> `PayableBillServiceImpl.java#updatePayableBill()` `L51 / L200`

- **入口类型**：HTTP 接口
- **用途**：修改付款单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 单据必须存在。
  - 仅允许 `billStatus = INIT(0)` 或 `INFIRM(3)` 时修改。
- **业务逻辑**：
  1. 重新按 `bizType` 回填业务属性。
  2. 重新按明细重算 `totalAmount`。
  3. 更新主表。
  4. 删除旧明细并全量重建。

### confirm
> `PayableBillController.java#confirm()` -> `PayableBillServiceImpl.java#confirm()` `L113 / L339`

- **入口类型**：HTTP 接口
- **用途**：审核付款单，并正式出账。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **状态变更**：
  - `wh_payable_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedTime/approvedMan`
- **业务逻辑**：
  1. 校验付款单存在、账户存在。
  2. 取 `diffBalance = totalAmount`，按现金钱包口径给账户钱包做正向增加。
  3. 写 `wh_wallet_log`，审核场景的 `accountingType = DEBT`。
  4. 调用 `doShopBankLog(payable, CONFIRM)`：
     - 按每条明细 `bankCode` 找网点银行卡
     - `balanceDiff = -detail.amount`
     - 扣减 `wh_shop_bank.balance`
     - 刷新银行日汇总
     - 写入 `wh_shop_bank_log`
  5. 刷新 `wh_wallet_daily`。
- **关键结论**：
  - 审核付款单会同时影响“银行卡余额”和“钱包余额”两条资金链。
  - 银行侧是出账口径，审核时扣余额；钱包侧则把该笔付款金额累计到账户现金钱包。

### unConfirm
> `PayableBillController.java#unConfirm()` -> `PayableBillServiceImpl.java#unConfirm()` `L121 / L498`

- **入口类型**：HTTP 接口
- **用途**：反审已审核付款单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 当前状态必须是 `CONFIRM(2)`。
  - 若 `payDate` 跨日，必须拥有 `PAYABLE_BILL_CROSS_DAYS` 权限。
- **状态变更**：
  - `wh_payable_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedTime/approvedMan`
- **业务逻辑**：
  1. 取 `diffBalance = -totalAmount`，反向扣回钱包余额。
  2. 写 `wh_wallet_log`，反审场景的 `accountingType = DEPOSIT`。
  3. 调用 `doShopBankLog(payable, INFIRM)`：
     - 因内部判断 `balanceDiff = amount`，会把银行余额加回去
     - 同步刷新银行日汇总和银行日志
  4. 刷新 `wh_wallet_daily`。
- **关键结论**：
  - 反审不是只改单头，而是完整回滚审核时的双资金链。
  - 付款单与收款单不同，反审后进入独立 `INFIRM(3)` 状态，不是直接回到 `INIT`。

### discard
> `PayableBillController.java#discard()` -> `PayableBillServiceImpl.java#discard()` `L129 / L583`

- **入口类型**：HTTP 接口
- **用途**：作废付款单
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 仅禁止作废 `CONFIRM(2)` 状态。
- **业务逻辑**：
  1. 若单据不存在则报错。
  2. 若当前状态是 `CONFIRM`，拒绝作废。
  3. 否则直接把 `bill_status -> DISCARD(4)`。

### importExcel
> `PayableBillController.java#importExcel()` -> `PayableBillServiceImpl.java#importExcel()` `L153 / L607`

- **入口类型**：HTTP 导入
- **用途**：批量导入付款单
- **业务逻辑**：
  1. 校验日期、单据号、客户名、金额非空。
  2. 以导入文件中的 `billNo` 分组。
  3. 用业务员、业务分区、客户/供应商名称补齐头信息。
  4. 系统重新生成内部付款单号，不沿用导入单号。
  5. 当前实现固定 `shopId = 1010L`，并按导入顺序生成明细。

## 公共规则

### 付款单是收款单的镜像资金流
1. 收款单审核：银行加、钱包减。
2. 付款单审核：银行减、钱包加。
3. 两者反审都按相反方向完整回滚。

### 付款单走标准 BillStatus 流
1. `INIT(0)`：未审
2. `CONFIRM(2)`：已审
3. `INFIRM(3)`：已反审
4. `DISCARD(4)`：已作废

### 付款对象由 `bizType` 决定
1. `2701(F_P_CUSTOMER)`：付客户款
2. `2702(F_P_SUPPLIER)`：付供应商款
3. `2703(F_P_REIMBURSEMENT)`：费用报销
4. 其中费用报销走的仍是“客户/账户”校验分支，不是独立费用对象分支。

### 表头金额以后端明细重算为准
1. `calcAndCheckSubmitValues()` 会把所有明细 `amount` 求和后回写 `totalAmount`。
2. 若和前端提交金额不一致，只记 warn 日志，不拒绝保存。

## 当前结论
- 付款单是“银行卡余额扣减 + 钱包余额增加”的双账本驱动入口，与收款单形成镜像关系。
- 付款单没有分账分支，状态流采用标准 `INIT -> CONFIRM -> INFIRM/DISCARD`。
- 钱包日志在审核与反审场景使用了不同 `accountingType`，后续若统一资金语义，必须把日志口径一起校验。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deletePayableBill` | 付款单 / 付款单明细 | 仅物理删除主表，未看到状态校验，也未同步删除明细与资金侧痕迹 | 待确认 | 确认接口是否仍对外开放，是否应限制为草稿态并补齐级联清理 |
| ⚠️ | `confirm` | 付款单 | 代码只拦“已审不能重复审核”，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()`，把可审核状态收敛到未审/已反审 |
| ⚠️ | `handleBills` | 付款单 / 导入 | 导入实现里固定 `shopId = 1010L`，未真正按导入网点映射，跨网点导入可能落错付款网点 | 待确认 | 确认该常量是否只是临时实现，是否应恢复按网点名称映射 |
