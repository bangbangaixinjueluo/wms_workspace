# 辅料类别

## 1. 模块定位

- 对应表：`wh_accessories_class`
- 入口：`AccessoriesClassController`、`AccessoriesClassServiceImpl`
- 角色：辅料类别主数据，承接编码、名称与批量导入

## 2. 核心职责

- 维护辅料类别编码与名称
- 提供 `getListByCodes(...)` 给导入或映射链按编码回查
- 为辅料商品、库存和单据提供类别语义

## 3. 代码结论

- `create(...)` 直接插入，未见额外唯一性保护
- `update(...)` 只校验 `id` 是否存在
- `batchSave(...)` 为空则返回，否则直接批量插入
- `getListByCodes(...)` 是这层目前最明确的下游调用能力

## 4. 关注点

- `⚠️` 删除链当前没看到对辅料商品、库存、历史单据的引用保护
- `⚠️` `batchSave(...)` 没看到按 `code` 去重，导入重复风险需要确认
- `⚠️` 当前唯一性更多依赖上游输入规范，不像 SKU/材质那样有明显业务宿主层保护
