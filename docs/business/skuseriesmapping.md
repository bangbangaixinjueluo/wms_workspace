# 系列款式映射模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skuseriesmapping` 是系列与 SKU 的映射主数据，不是普通关系表。建档时支持一次给一个系列挂多款 SKU。

## 关键入口
- Service: [SkuSeriesMappingServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skuseriesmapping/SkuSeriesMappingServiceImpl.java)

## 真实行为
- `createSkuSeriesMapping(createReqVO)`
  - 会遍历 `skuIds`，逐个校验 SKU 是否存在、系列内是否重复，再逐条插入。
- `getSkuSeriesMappingPage(pageVO)`
  - 系列映射分页走自定义 mapper `getPage(...)`。

## 风险与待确认
1. `⚠️` `validateSkuSeriesMappingExists(...)` 里异常被注释掉了，不存在记录时不会明确报错。
2. `⚠️` `createSkuSeriesMapping(...)` 里 `skuSeriesMapping.setSkuNo(createReqVO.getSkuNo())` 使用的是请求对象字段，不是当前循环 SKU 的真实 `skuNo`，需要确认口径。
