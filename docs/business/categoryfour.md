# 四级分类模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`categoryfour` 是品类结构链的四级分类主数据，不是普通字典。它带缓存，并且分页展示时要回查上级品类名。

## 关键入口
- Service: [CategoryFourServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/categoryfour/CategoryFourServiceImpl.java)

## 真实行为
- `getCategoryFourPage(...)`
  - 分页时会回查 `category` 补上一级品类名。
- `getCategoryFour(id)`
  - 走 `categoryFour` 缓存。

## 风险与待确认
1. `⚠️` 删除仍是物理删除。
2. `⚠️` 更新/删除只清单条 id 缓存，若存在列表级缓存要确认是否同步失效。
