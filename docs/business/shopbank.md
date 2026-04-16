# 网点银行卡

## 1. 模块定位

- 对应表：`wh_shop_bank`、`wh_shop_bank_daily`
- 入口：`ShopBankController`、`ShopBankServiceImpl`
- 角色：网点银行卡余额宿主，承接收款单、付款单、内部转账和资金日报

## 2. 主链结论

- 网点银行卡不是普通资料表，而是银行侧余额主账
- 真正关键动作是 `transferBalance(...)` 和 `refreshDaily(...)`
- 它是收款单/付款单银行侧变动的共同基础宿主

## 3. 核心职责

- 维护银行卡基础信息与余额
- 按 `bankCode` 回查银行卡
- 转账时同时更新付款方和收款方余额
- 生成双边银行流水日志
- 按业务日期补刷银行日结 `shop_bank_daily`

## 4. 代码结论

- `transferBalance(...)`：
  - 先扣付款方余额，再加收款方余额；
  - 更新时按“旧余额相等”做乐观锁；
  - 同时为双方各写一条 `ShopBankLogDO`
- `refreshDaily(...)`：
  - 对非当日、非零变动补刷日结；
  - 只允许回刷近 3 个月；
  - 使用 Redis 锁串行化

## 5. 关注点

- `⚠️ deleteShopBank(...)` 是物理删除，未见对银行流水、收付款单历史引用的保护
- `⚠️ transferBalance(...)` 先扣后加，虽然在事务内，但仍需重点回归异常补偿
- `⚠️ refreshDaily(...)` 只对非当日动作补刷，改日报口径前要先确认这个前提
