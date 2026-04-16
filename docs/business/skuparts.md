# 款式配件信息模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skuparts` 是 SKU 配件卫星表，承接款式级配件信息。

## 关键入口
- Service: [SkuPartsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skuparts/SkuPartsServiceImpl.java)

## 风险与待确认
1. `⚠️` 当前是薄 CRUD，真实聚合能力主要在 SKU 宿主层。
2. `⚠️` 删除仍是物理删除。
