# 单件标签共享

## 模块定位
- 对应表：`wh_item_share`
- 入口：`ItemShareController`、`ItemShareServiceImpl`
- 这不是独立业务单，而是“单件共享关系 + 同步任务回执”宿主，承接单件对外共享后的客户归属、来源单号和同步状态。

## 主数据职责
- 关键字段包括：
  - `itemId`
  - `customerId`
  - `billNo`
  - `bizType`
  - `deptId`
  - `syncId`
  - `syncStatus`
  - `syncMsg`
- 业务唯一口径更接近“某单件在某客户侧的一次共享占用”，并且会继续挂接外部同步任务结果。

## 主要业务动作
- `createItemShare(...)` / `updateItemShare(...)` / `deleteItemShare(...)` 提供基础维护。
- `getItemShareByCustomerIdAndItemId(customerId, itemId)` 说明它会被当作“单件是否已共享给某客户”的快速判定表。
- `itemExists(itemId)` 说明单件维度存在性校验直接落在这张表上。
- `getItemShareBySyncId(itemId, syncId)` 和 `updateItemShareBySyncId(itemId, syncId, ...)` 说明同步回执不是只记在 `wh_sync_task`，还会回写到单件共享关系本身。

## 同步链语义
- `syncId` 对接 `wh_sync_task.id` 一类外部同步任务主键。
- `syncStatus` / `syncMsg` 是共享关系上的最近一次同步结果快照。
- 这张表因此同时承担：
  - 单件共享占用关系
  - 对外同步结果投影

## 关联模块
- `item`：共享对象来源。
- `customer`：共享目标客户。
- `synctask`：对外同步任务宿主。
- `stockshare`：旧版库存共享汇总层会结合单件共享结果做同步判定。

## 关键结论
- 单件标签共享不是普通关系表，而是旧版共享库存链里“单件粒度的同步宿主”。
- 它把“谁的单件、共享给谁、来源哪张单、同步是否成功”四类语义压在了一张表里。

## 风险与疑点
- `⚠️` 删除当前是物理删除，没看到对同步历史或共享来源业务的保护。
- `⚠️` `itemExists(itemId)` 只按 `itemId` 判断，若业务上允许同一单件多客户多批次共享，存在语义过宽风险。
- `⚠️` `getItemShareBySyncId(itemId, syncId)` 说明共享结果强依赖同步任务回写；若回调丢失，这张表会停在旧状态。
