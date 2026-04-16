# 辅料库存

## 1. 模块定位

- 对应表：`wh_accessory_stock`
- 入口：`AccessoryStockController`、`AccessoryStockServiceImpl`
- 角色：辅料库存台账主表

## 2. 核心职责

- 维护辅料库存当前量
- 支持按 `shopNo/wareNo/categoryNo/goodsNo` 回查库存记录
- 提供分页、导出与基础 CRUD

## 3. 代码结论

- 当前 service 是纯 CRUD
- 没看到由辅料入库单/出库单审核自动驱动的库存增减逻辑
- 这张表更像“库存结果表”，但当前执行链没有明显写入它

## 4. 关注点

- `⚠️ deleteAccessoryStock(...)` 是物理删除，未见库存日志或历史单据保护
- `⚠️` 如果入库/出库审核没有真实改这张表，当前库存口径可能依赖外部同步或尚未接线的业务层
