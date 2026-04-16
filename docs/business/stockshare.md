# 库存共享

## 模块定位
- 对应表：`wh_stock_share`
- 入口：`StockShareController`、`StockShareServiceImpl`、`StockShareMapper`
- 这不是前面那套 `wh_share_stock` 新共享链，而是旧版“共享库存汇总 + 锁定 + 对账同步”宿主。

## 主数据职责
- 关键字段包括：
  - `customerId`
  - `skuId / skuNo`
  - `modelNo`
  - `segmentNo`
  - `specs`
  - `num`
  - `numRemote`
  - `numSync`
  - `numLock`
  - `syncStatus`
- 核心口径是“某客户某款某模号某规格在旧共享体系里的本地库存、远端库存、待同步量和锁定量”。

## 主要业务动作
- `lockByCustomerOrder(orderRespVO, isLock)` 会按客户订单锁定或释放共享库存，说明它直接承接订单占用。
- `lockStockToOut(order)` 说明共享锁定量会继续下沉到出库动作。
- `updateStockShareNum(id, waitingNum)` 会重算待同步数量。
- `getStockShareVOByModelNoAndSpecs(...)` 说明 `modelNo + specs` 是查询共享库存的关键维度。
- 分页和导出走 `selectStockList(...) / selectStockListCount(...)` 自定义 SQL，不只是基础 CRUD。

## 对外同步入口
- 控制层 `/sync/inout` -> `syncTaskService.stockSyncInOut(syncReqVO)`，负责共享出入库同步。
- 控制层 `/sync/check` -> `syncTaskService.stockReport(syncReqVO)`，负责共享库存核对。
- 控制层 `/sync/diff` -> `syncTaskService.stockInOutDiff(syncReqVO)`，负责结余差额出入库同步。
- 说明这张表不只是报表快照，而是旧版对外库存同步的业务宿主。

## 与新共享链的边界
- `wh_stock_share` 更像旧体系，围绕：
  - `num / numRemote / numSync / numLock`
  - 同步状态
  - 订单锁定
- `wh_share_stock` 新体系则围绕：
  - `shareStock / surplusStock / lockStock / diffStock`
  - 流水宿主
  - API 痕迹
  - 日报
- 两套表都在做共享库存，但建模层次和宿主职责不同，当前代码里存在并存期。

## 关联模块
- `synctask`：旧版共享库存同步入口。
- `itemshare`：单件共享粒度结果。
- `stocksharelog`：库存共享数量变动日志。
- `customerorder`：锁定共享库存的重要来源。

## 关键结论
- 库存共享不是简单汇总表，而是旧版共享库存体系的中心宿主。
- 它直接承接“订单锁定、出入库同步、远端对账、差额同步”四条链，业务地位明显高于普通查询表。

## 风险与疑点
- `⚠️` 旧版 `wh_stock_share` 和新版 `wh_share_stock` 并存，若两套口径同时被用到，容易产生共享库存双主数据漂移。
- `⚠️` 删除当前是物理删除，没看到对同步日志、订单锁定和历史对账结果的保护。
- `⚠️` `syncStatus` 同时出现在主表、日志表、同步任务表里，真实“最终状态主来源”需要继续确认。
