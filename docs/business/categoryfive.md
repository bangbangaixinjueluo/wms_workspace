# 五级分类模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`categoryfive` 是五级分类主数据宿主，不只是字典。它支持按编号/名称/上级品类回查，并被 SKU 生态广泛引用。

## 关键入口
- Service: [CategoryFiveServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/categoryfive/CategoryFiveServiceImpl.java)

## 真实行为
- `createCategoryFive(...)`
  - 建档时会校验“同品类下五级分类编号唯一”。
- `getMapByNos(setNos)` / `getDOMapByNos(...)`
  - 支持按五级分类编号批量映射。
- `getSimpleList()`
  - 提供轻量清单接口。

## 风险与待确认
1. `⚠️` 删除仍是物理删除。
2. `⚠️` `getSimpleList()` 当前未显式按状态过滤，只是 select 出了状态字段。
