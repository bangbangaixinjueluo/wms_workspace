# 套式映射模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skusetmapping` 是套式宿主链，不是简单关系表。它负责生成套式编码、校验套内 SKU 组合唯一，并反写 SKU 主档的 `setNo/setName`。

## 关键入口
- Service: [SkuSetMappingServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skusetmapping/SkuSetMappingServiceImpl.java)

## 真实行为
- `createSkuSetMapping(...)`
  - 生成 `TS{year}{seq}` 套式编号，校验套内 SKU 组合不重复，并回写 SKU 主档及缓存。
- `updateSkuSetMapping(...)`
  - 先清旧 SKU 的 `setNo/setName`，再重建新套式挂接并刷新缓存。
- `deleteSkuSetMapping(id)`
  - 只删套式映射本身。

## 风险与待确认
1. `⚠️` 删除套式映射时没有同步清理已回写到 SKU 主档的 `setNo/setName`。
2. `⚠️` `validateSkuSetMappingExists(...)` 里异常被注释掉了，不存在记录时不会明确报错。
3. `⚠️` 套式编号按年份 + 5 位流水生成，跨年重置逻辑需长期关注。
