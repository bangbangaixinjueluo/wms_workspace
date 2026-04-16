# 银行卡日结模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`shopbankdaily` 是银行卡余额日结和差异核对宿主。网点银行卡的昨日结余、当日流水推演、差异表和告警邮件都在这层。

## 关键入口
- Service: [ShopBankDailyServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/shopbankdaily/ShopBankDailyServiceImpl.java)

## 真实行为
- `genShopBankDaily()`
  - 以前一日为统计日，按当前银行卡余额生成日结；先删旧日结再批量重建。
- `checkBankDailyDiff(params)`
  - 按日期范围逐日核对银行卡日结。
- `checkDiff(statDate)`
  - 用昨日 daily + 当日 bank log 推演理论余额，再和当日 real daily 比较，生成 `shop_bank_daily_diff`，并异步发告警邮件。

## 在主链中的作用
- `shopbank/shopbanklog`
  - 银行卡主余额和流水在主链；日结层负责沉淀和差异核对。

## 风险与待确认
1. `⚠️` `genShopBankDaily()` 采用先删后建，失败时会影响整天银行卡日结。
2. `⚠️` 差异核对依赖 `bankCode` 聚合，同一银行卡号若出现历史重复记录，要确认合并语义是否稳定。
3. `⚠️` 告警邮件是异步发送，失败不会阻断差异表落库。
