# 存欠账户

## 1. 模块定位

- 对应表：`wh_account`
- 入口：`AccountController`、`AccountServiceImpl`
- 角色：客户/供应商资金主账户宿主，承接账户编码、账户类型和钱包归属

## 2. 核心职责

- 维护 `accountCode/accountName/accountType`
- 按客户、供应商、账户 ID 反查账户
- 创建或更新账户时生成 `A + 6位流水` 财务编码
- 删除账户时级联删除该账户下的钱包

## 3. 代码结论

- `createAccount(...)` 会校验 `accountCode` 不重复
- `createOrUpdateAccount(...)` 是客户/供应商主档更常见的接线入口
- `deleteAccount(...)` 不只删账户，还会调用 `walletMapper.deleteWalletByAccountId(id)`
- `getAccountAndCheckByCustomerId/getAccountAndCheckBySupplierId` 是财务链里的常用承接入口

## 4. 关注点

- `⚠️ deleteAccount(...)` 会直接级联删钱包，没看到对钱包日志、业务单据、历史财务数据的保护
- `⚠️` `createOrUpdateAccount(...)` 里新建走自动编码、改单只在名称变化时更新，字段漂移需要确认
- `⚠️` 账户是钱包和客户/供应商之间的桥，后续不要把它当成普通资料表改
