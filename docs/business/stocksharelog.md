# 库存共享日志

## 模块定位
- 对应表：`wh_stock_share_log`
- 入口：`StockShareLogController`、`StockShareLogServiceImpl`
- 这不是普通操作日志，而是旧版共享库存链里的“数量变动明细 + 同步回执投影”宿主。

## 主数据职责
- 关键字段包括：
  - `stockId`
  - `customerId`
  - `skuId`
  - `modelNo`
  - `specs`
  - `numBefore`
  - `numChange`
  - `numAfter`
  - `syncId`
  - `syncStatus`
  - `remark`
- 它保留了共享库存调整前后量，因此更接近共享库存事件表，而不是纯描述性日志。

## 主要业务动作
- `createStockShareLog(...)` / `updateStockShareLog(...)` / `deleteStockShareLog(...)` 提供基础维护。
- `getStockShareLogVO(...)` / `getStockShareLogVOPage(...)` 会补：
  - `customerName`
  - `skuName`
- `updateSyncStatus(id, syncId, syncStatus)` 说明单笔共享日志会被同步任务结果反向回写。

## 同步链语义
- `syncId` 关联旧版对外同步任务。
- `syncStatus` 代表该笔库存变化是否已经成功推送或回执。
- 因为主表 `wh_stock_share` 只有当前快照，这张日志表承担了历史变动链的追溯责任。

## 关联模块
- `stockshare`：库存共享快照宿主。
- `synctask`：同步任务与回调结果来源。
- `customer` / `sku`：查询展示补齐字段来源。

## 关键结论
- 库存共享日志不是普通后台日志，而是旧版共享库存的事件明细层。
- 如果要追共享库存为什么变、哪次同步失败，真正应该落回这张表，而不是只看主表。

## 风险与疑点
- `⚠️` 删除当前是物理删除，历史库存变化链可能被直接抹掉。
- `⚠️` `syncStatus` 只是一笔日志上的投影值，若任务重试多次，未必能完整表达全过程。
- `⚠️` 分页和导出查询只显式按 `remark` 过滤，业务检索维度偏弱，更多依赖上层 VO 组装。
