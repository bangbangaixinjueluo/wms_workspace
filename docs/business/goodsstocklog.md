# 成品库存日志

## 1. 模块定位

- 对应表：`wh_goods_stock_log`
- 入口：`GoodsStockLogController`、`GoodsStockLogServiceImpl`
- 角色：成品库存事件表

## 2. 核心职责

- 记录成品库存变更日志
- 分页时反查库存记录和 SKU 基础信息
- 支持批量插入日志

## 3. 代码结论

- 当前主要能力是查询与 `insertBatchLogs(...)`
- 分页展示需要 `stockId -> goodsStock -> skuNo -> sku`
- 它更多是库存宿主的配套事件层

## 4. 关注点

- `⚠️` 日志展示强依赖 `stockId` 还能回查到库存主表，删库存会影响日志补数
- `⚠️` 当前未显式暴露删除，但若后续补删，需先确认审计保护
