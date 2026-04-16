# 钱包

## 1. 模块定位

- 对应表：`wh_wallet`
- 入口：`WalletController`、`WalletServiceImpl`
- 角色：客户/供应商资金链的真实宿主，承接现金钱包、材质钱包、结算类别、余额变更和授信检查

## 2. 主链结论

- 钱包不是薄主数据，而是收款单、付款单、调账单、维修链、来料出料链等资金回写的统一落点
- 现金钱包与材质钱包共表管理，差异主要由 `walletTypeNo/materialNo/settleClass` 识别
- 真正的余额变更宿主在 `doChangeWallet(...)`

## 3. 核心职责

- 创建钱包时按钱包类型校验 `materialNo`
- 保证 `(accountId, materialNo, settleClass)` 唯一
- 按客户/供应商返回有效钱包列表
- 按账户 + 钱包类型聚合钱包
- 执行钱包余额变更并批量生成钱包日志

## 4. 代码结论

- `createWallet(...)`：
  - 现金钱包强制 `materialNo = "-"`；
  - 非现金钱包必须带有效原料编码；
  - 自动填 `walletUnit`
- `checkUniqueKey(...)` 明确把“账户 + 原料 + 结算类别”作为唯一键
- `doChangeWallet(...)`：
  - 以 `WalletBaseDTO` 为宿主；
  - 支持不存在钱包时自动建钱包；
  - 同时生成 `WalletLogDO`；
  - 对客户授信检查曾有接线，但目前相关校验被注释

## 5. 关注点

- `⚠️ deleteWallet(...)` 是物理删除，未见对钱包日志和历史财务单据的保护
- `⚠️ doChangeWallet(...)` 是大量财务模块共同依赖的核心入口，修改一处会连动整条资金链
- `⚠️` 客户授信余额校验代码当前有注释痕迹，真实启用边界需要确认
