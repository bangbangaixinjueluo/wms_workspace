# 钱包日志

## 1. 模块定位

- 对应表：`wh_wallet_log`
- 入口：`WalletLogController`、`WalletLogServiceImpl`
- 角色：钱包变更事件表，承接业务单号、钱包余额前后值、网点/仓库补数与报表查询

## 2. 核心职责

- 记录钱包每次资金/重量变更日志
- 支持按 `bizNo`、`walletId`、业务日期等维度查询
- 分页时补 `walletName/settleClass/shopName/wareName`
- 支持批量插入和按 `billNo` 删除日志

## 3. 代码结论

- service 除 CRUD 外，真正关键的是：
  - `insertBatch(...)`
  - `deleteByBillNo(...)`
  - `getByBizDateForAccountId(...)`
- 钱包日志分页需要回查钱包、仓库、网点再做展示补数
- `bizNo` 已经是跨财务单据的重要追踪键

## 4. 关注点

- `⚠️ deleteWalletLog(...)` 和 `deleteByBillNo(...)` 都是物理删除，历史追溯保护偏弱
- `⚠️` 钱包日志是很多反审链的清理落点，改删除逻辑前要整链回归
