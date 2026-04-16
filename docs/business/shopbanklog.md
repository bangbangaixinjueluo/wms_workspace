# 网点银行卡日志

## 1. 模块定位

- 对应表：`wh_shop_bank_log`
- 入口：`ShopBankLogController`、`ShopBankLogServiceImpl`
- 角色：网点银行卡事件日志表，承接银行侧业务单号、余额前后值和网点/账户名补数

## 2. 核心职责

- 记录银行卡入账、出账、转账日志
- 按 `bizNo` 反查银行侧资金事件
- 分页/导出时补 `accountName/shopName`

## 3. 代码结论

- service 本身是薄 CRUD
- 真实业务写入主要来自 `ShopBankService.transferBalance(...)` 和收款/付款链
- `fillData(...)` 会按 `bankCode -> shopBank -> accountName` 以及 `shopId -> shopName` 做展示补数

## 4. 关注点

- `⚠️ deleteShopBankLog(...)` 是物理删除，银行侧历史追溯保护偏弱
- `⚠️` 页面展示强依赖 `bankCode` 还能反查到银行卡主表，删主表后日志展示可能缺字段
