# 钱包授信额度模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`walletcredit` 是账户授信额度主档，不是普通资料表。它约束的是账户级授信额度唯一性与状态化查询。

## 关键入口
- Service: [WalletCreditServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/walletcredit/WalletCreditServiceImpl.java)

## 真实行为
- `getCreditByAccountIdAndStatus(accountId, status, isCheck)`
  - 按账户和状态查授信额度。
- `createWalletCredit(...)`
  - 建档前会校验同账户只能存在一条授信记录。
- `updateWalletCredit(...)`
  - 修改时也会校验账户唯一性。

## 在主链中的作用
- 账户/钱包信用链
  - 承接账户级授信额度。

## 风险与待确认
1. `⚠️` 当前按“账户只能一条授信”建模，若未来要支持多额度档或历史版本，需要改模型。
2. `⚠️` `deleteWalletCredit(...)` 仍是物理删除。
