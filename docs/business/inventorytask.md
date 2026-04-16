# 单件盘点任务

## 1. 模块定位

- 对应表：`wh_inventory_task`、`wh_inventory_task_detail`
- 入口：`InventoryTaskController`、`InventoryBusiness`、`InventoryTaskServiceImpl`
- 角色：单件盘点单下面的任务宿主，承接任务批次、任务明细扫描状态和盘盈池

## 2. 主链结论

- 它不是独立盘点单，而是 `iteminventorybill` 下面的任务层
- 主要动作是：建任务、提交任务、重盘、保存盘盈、扫描状态推进

## 3. 核心职责

- 保存盘点任务单头与任务明细
- 按任务维度维护 `NOT_SCAN / SCANNED / PROFIT` 这类扫描状态
- 支持按任务重置扫描状态
- 支持任务级盘盈数据重建

## 4. 代码结论

- `save(...) + saveDetailBatch(...)` 是任务真实落表入口
- `updateDetailStatus(...)` 会只更新状态发生变化的明细
- `saveInventoryProfit(...)` 会先清空历史盘盈，再重建新的盘盈明细
- `resetInventory(...)` 只把 `SCANNED` 重置回 `NOT_SCAN`

## 5. 关注点

- `⚠️ saveInventoryProfit(...)` 是“先删后建”模式，中途失败会影响盘盈池一致性
- `⚠️ resetInventory(...)` 只重置已扫描，不处理盘盈明细，任务重盘边界需确认
- `⚠️` 任务宿主和盘点单宿主是两层结构，后续不要只改其中一层状态
