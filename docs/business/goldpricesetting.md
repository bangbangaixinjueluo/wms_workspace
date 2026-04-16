# 金价配置

## 模块定位
- 入口：`GoldPriceSettingController`、`GoldPriceSettingBusiness`、`GoldPriceSettingServiceImpl`
- 宿主表：`wh_gold_price_setting`、`wh_gold_price_log`、`wh_gold_price_rule`
- 这不是普通配置表，而是“我的金价配置 + 金价调整日志 + 时段规则”三层组合宿主。

## 关键入口
- `/wh/gold-price/setting`
- `/wh/gold-price/custom`
- `/wh/gold-price/rule/create`
- `/wh/gold-price/rule/update`
- `/wh/gold-price/rule/delete/{ruleId}`
- `/wh/gold-price/rule/list`

## 真实行为
- `setting(...)` 只配置 `GoldCodeEnum.INVEST` 这一路“我的金价”。
- `getCustom()` 会同时拼三组口径：
  - 上金所公告价 `shGoldPriceApi.getAu9999()`
  - 配置中心里的加价项 `gold_price_increase`
  - 本系统维护的“我的金价”
- `saveOrUpdate(...)` 按 `code` 查是否已存在，存在则更新，不存在则插入；每次保存后都追加一条 `wh_gold_price_log`。
- 规则层 `wh_gold_price_rule` 支持独立增删改查。
- `createRule/updateRule` 会校验时间段不能交叉，且规则号按 `GPR` 生成。
- `getRuleByTime(now)` 说明时段规则是会被运行时按当前时间命中的，不只是后台展示资料。

## 在主链中的作用
- 给系统提供“公告价 / 建议价 / 我的价”三套金价口径。
- 给时段型金价规则提供可运行的规则池。
- 给金价变更留审计日志。

## 关键结论
- 这是价格规则宿主，不是普通参数配置。
- `gold_price_increase + Au9999 + 我的金价` 已经形成一套组合定价口径。

## 风险与待确认
- `⚠️` `saveOrUpdate(...)` 更新已存在记录时，`addLog(convert)` 直接用 `convert.getId()` 写日志；若调用方没把更新后 `id` 正确带回，日志上的 `settingId` 可能为空或错误。
- `⚠️` 规则删除目前是物理删除，未见对历史命中记录的保护。
- `⚠️` 时间段重叠校验只检查起止点是否落在已有区间内，若出现“新规则完全包住旧规则”这类场景，边界要继续确认。
