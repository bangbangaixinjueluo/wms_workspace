# 辅料物品分类

## 1. 模块定位

- 对应表：`wh_accessory_category`
- 入口：`AccessoryCategoryController`、`AccessoryCategoryServiceImpl`
- 角色：辅料商品分类字典，按 `categoryNo` 提供分类映射

## 2. 核心职责

- 维护辅料分类主数据
- 提供 `getAccessoryCategoryByCategoryNo(...)` 给辅料商品、库存和单据回查

## 3. 代码结论

- `create/update/delete` 都是典型 CRUD
- `update/delete` 只校验主键存在
- 当前没看到与辅料商品、库存、单据的联动清理或回刷

## 4. 关注点

- `⚠️ deleteAccessoryCategory(...)` 是物理删除，没看到对 `goods/categoryNo` 的引用保护
- `⚠️` `categoryNo` 是否全局唯一主要靠 mapper/表约束，service 层没有显式守卫
