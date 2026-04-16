# 辅料物品档案

## 1. 模块定位

- 对应表：`wh_accessory_goods`
- 入口：`AccessoryGoodsController`、`AccessoryGoodsServiceImpl`
- 角色：辅料商品主档，连接辅料分类、库存、入库、出库、领用明细

## 2. 核心职责

- 维护辅料商品编号、分类编号与基础属性
- 提供 `getAccessoryGoodsByCategoryNo(...)`、`getAccessoryGoodsByGoodsNo(...)` 按分类号或商品号回查
- 作为辅料单据明细里的 `accessoryNo/accessoryName` 来源池

## 3. 代码结论

- 服务层仍然是薄 CRUD
- `goodsNo` 和 `categoryNo` 已经是辅料执行链里的关键映射字段
- 当前没看到改档后回刷历史库存或历史单据快照的逻辑

## 4. 关注点

- `⚠️ deleteAccessoryGoods(...)` 是物理删除，未见库存、库存日志、单据明细引用保护
- `⚠️` 服务层没显式校验分类存在，`categoryNo` 口径更依赖上游输入正确
- `⚠️` 执行链明细大量使用辅料编号快照，后续改名与历史展示口径可能漂移
