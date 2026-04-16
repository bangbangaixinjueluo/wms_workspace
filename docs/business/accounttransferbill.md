# 结价单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`accounttransferbill` 虽然代码名叫“账务转移单”，但真实业务注释和执行链都指向“结价单/结价审核”。它不是薄财务录入页，而是“账户当前欠款/欠料快照 + 审核双钱包回写 + 业务员权限过滤”的财务执行宿主。

## 关键入口
- Controller: [AccountTransferBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/accounttransferbill/AccountTransferBillController.java)
- Service: [AccountTransferBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/accounttransferbill/AccountTransferBillServiceImpl.java)

## 真实行为
### 1. 建单会先冻结账户现状
`createAccountTransferBill(...)` 会：
1. 生成 `FINANCE_STATEMENT_OFFER` 前缀单号。
2. 按 `bizType` 校验客户结价或供应商结价。
3. 客户结价时补齐 `customer/businessArea/businessStaff/businessManage`。
4. 通过 `walletService.getStockNow(...)` 回填当前账户的 `stockWeightNow/stockMoneyNow`。
5. 重算 `settleAmount = settleWeight * settlePrice`，并在前后端值不一致时打 warn 日志。

### 2. 审核不是改单头状态，而是双钱包回写
`confirm(id)` 的真实动作是：
1. 校验单据未审核、账户存在、金额和重量不为空、原料存在。
2. 现金侧：
   - `materialNo = "-"`、`walletTypeNo = CASH`
   - `balanceDiff = settleAmount`
   - `accountingType = DEBT`
   - 语义是“欠款增加”
3. 材质侧：
   - `materialNo = material.materialNo`
   - `balanceDiff = -settleWeight`
   - `accountingType = DEPOSIT`
   - 语义是“欠料减少”
4. 两边都通过 `walletService.createWalletByAccountNew(...)` 落钱包和钱包日志。
5. 最后才把单头推进到 `CONFIRM`，并写 `approvedMan/approvedTime`。

### 3. 反审是整条镜像回滚
`unConfirm(id)` 会：
1. 只允许 `CONFIRM` 反审。
2. 校验跨日权限 `ACCOUNT_TRANSFER_BILL_CROSS_DAYS`。
3. 现金侧做 `balanceDiff = -settleAmount`、`accountingType = DEPOSIT`。
4. 材质侧做 `balanceDiff = settleWeight`、`accountingType = DEBT`。
5. 单头状态改成 `INFIRM`，并清空审核人和审核时间。

### 4. 查询页不是全员全量视图
`getPage(...)` 会按登录人角色自动收口：
- 业务经理：按 `areaId`
- 业务员/业务助理：按 `staffId`

## 在主链中的作用
- `account`
  - 单头只存 `customerId/supplierId`，真实资金落点仍然是账户。
- `wallet/walletlog`
  - 审核与反审都不直接改余额字段，而是统一走钱包入账入口。
- 客户档案
  - 客户结价会顺带把业务分区和业务员信息投影进单头，方便财务侧分区过滤。

## 当前结论
- 这张单据不是收款单/付款单那种“银行 + 钱包”双账链，而是纯钱包链。
- 审核语义可以概括为：“现金欠款增加，材质欠料减少”；反审则完全镜像。

## 风险与待确认
1. `⚠️` `deleteAccountTransferBill(...)` 当前直接物理删除，未见状态限制和钱包历史保护。
2. `⚠️` `discard(...)` 只拦已审核状态，没显式限制 `DISCARD` 等异常状态重复作废。
3. `⚠️` `confirm(...)` 只拦重复审核，未显式收紧到 `INIT/INFIRM` 白名单。
4. `⚠️` `setStockWeightAndMoney(...)` 在更新时用的是 `walletTypeNo` 传参，不是 `materialNo`，当前依赖两者同义，后续改口径容易串。

