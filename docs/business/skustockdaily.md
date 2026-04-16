# 款式库存日报模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skustockdaily` 不是简单历史表，而是 SKU 库存日报宿主。分页查询、日报初始化、库存差异、告警邮件和报表查询都在这层。

## 关键入口
- Service: [SkuStockDailyServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skustockdaily/SkuStockDailyServiceImpl.java)

## 真实行为
- `getSkuStockDailyVOPage(...)`
  - 通过 join wrapper 直接联 `sku/shop/warehouse/location` 做分页，并返回 summary。
- `getSkuStockDailyByTime(time)`
  - 按统计日回放日报，并补门店、仓库、材质等显示字段。
- `getSkuStockDailyByOptions(map)`
  - 提供报表口径查询。
- `initDaily()`
  - 初始化前一日 SKU 库存日报，会先删当日旧数据再重建。
- `getStockByWareName(...)`
  - 提供按仓名/材质名汇总的库存查询。

## 在主链中的作用
- `skustock`
  - 实时库存链通过回刷或初始化把结果沉淀到日报层。
- `stockreport`
  - 多条库存报表最终会读取日报层，而不是只看实时库存。

## 风险与待确认
1. `⚠️` `initDaily()` 会先删后建，若中途失败，整天日报可能被清空。
2. `⚠️` 查询层同时保留了 join 查询与旧版 `fillData(...)` 方案，后续字段扩展容易两套口径漂移。
3. `⚠️` `getSkuStockDailyByTime(...)` 在无数据时直接抛异常，不是返回空列表。
