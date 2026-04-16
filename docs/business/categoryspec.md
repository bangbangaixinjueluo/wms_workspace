# 品类规格模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`categoryspec` 是品类规格主数据，承接“某品类有哪些规格”的规则映射。

## 关键入口
- Service: [CategorySpecServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/categoryspec/CategorySpecServiceImpl.java)

## 真实行为
- `getCategorySpecPage(...)`
  - 分页时会回查上级品类补 `categoryName`。
- `getAll()`
  - 提供全量规格清单。

## 风险与待确认
1. `⚠️` 删除仍是物理删除。
2. `⚠️` 规格主数据已带缓存，但只按 id 清缓存。
