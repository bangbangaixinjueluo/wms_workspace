# 钱包日结模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`walletdaily` 不是普通日报表，而是钱包余额日结和差异核对宿主。钱包逐日余额、日志回放、差异对账和告警邮件都在这层。

## 关键入口
- Service: [WalletDailyServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/walletdaily/WalletDailyServiceImpl.java)

## 真实行为
- `refresh(daily)`
  - 按账户 + 钱包类型 + 结算类加 Redis 锁，把某一天之后的 wallet daily 全量递推刷新。
- `buildDaily(...)`
  - 若某日无 daily 记录则补建，有记录则累加更新。
- `checkWalletDailyDiff(params)`
  - 按日期范围检查钱包日结差异。
- `checkDiff(statDate)`
  - 用昨日 daily + 当日 walletlog 推演理论余额，再和当日 real daily 比较，生成 `wallet_daily_diff`，并异步发告警邮件。

## 在主链中的作用
- `wallet/walletlog`
  - 钱包真实余额与流水在主链；walletdaily 负责日结沉淀和对账。

## 风险与待确认
1. `⚠️` `refresh(...)` 只允许追溯 3 个月，超期补账会失败。
2. `⚠️` 差异检查强依赖 `walletLog.bizDate` 和 `daily.ids` 聚合，一旦历史日志缺失或 ids 漂移，对账会失真。
3. `⚠️` 告警邮件是异步发送，失败不会阻断差异表落库。
