# 辅料库存日志

## 1. 模块定位

- 对应表：`wh_accessory_stock_log`
- 入口：`AccessoryStockLogController`、`AccessoryStockLogServiceImpl`
- 角色：辅料库存日志台账表，按业务单号和辅料商品号留痕

## 2. 核心职责

- 记录辅料库存变更日志
- 支持按 `goodsNo`、`bizNo` 回查
- 提供分页、导出和基础 CRUD

## 3. 代码结论

- service 仍然是纯 CRUD
- 当前没看到辅料入库单/出库单审核显式写这张表
- `bizNo` 已经预留为业务单关联键，但执行链接线不明显

## 4. 关注点

- `⚠️ deleteAccessoryStockLog(...)` 是物理删除，历史追溯保护偏弱
- `⚠️` 如果主执行链没有真正写日志，这张表当前更像“预留日志宿主”
