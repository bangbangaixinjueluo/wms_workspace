# 款式库存模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skustock` 不是普通库存报表，而是 SKU 库存执行宿主。它承接 SKU/模号/规格/库位粒度的实时库存、共享库存联动、库存日志、日报回刷和锁控制。

## 关键入口
- Service: [SkuStockServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skustock/SkuStockServiceImpl.java)

## 真实行为
- `getPage(...)`
  - 按 `single + sku/model/specs` 做分组分页，不是直接查单表明细。
- `getPageDynCols(...)`
  - 提供动态列库存分页。
- `getInfo(reqVO)`
  - 返回 SKU 库存明细，并补门店、仓库、库位名称。
- `refreshSingleDaily(statDate, singleMap, increase)`
  - 回刷单件库存日报，并通过 Redisson 锁保护同键刷新。
- `getLogType(bilType, logTypeEnum)`
  - 按单据类型把日志归并成 `IN/OUT` 两类。

## 在主链中的作用
- `item / goods / sale / maintain / share`
  - 多条单件和成品执行链都会最终影响到 SKU 库存。
- `skustockdaily`
  - SKU 日报并不是独立计算，而是由实时库存链回刷或初始化。

## 风险与待确认
1. `⚠️` `refreshSingleDaily(...)` 只允许追溯 3 个月，跨更早账期会直接失败。
2. `⚠️` 日报回刷依赖 Redis 锁和运行时增减方向判断，失败时不只是报表误差，还会影响后续差异链。
3. `⚠️` `getLogType(...)` 把多种业务单据收敛成同一 `IN/OUT` 类型，若后续需要更细粒度日志语义，要同步检查整条库存日志链。
