# 单件盘点单批次

## 模块定位
- 入口：`ItemInventoryBillBatchController`、`ItemInventoryBillBatchServiceImpl`
- 宿主表：`wh_item_inventory_bill_batch`
- 这是单件盘点单的批次宿主层。

## 关键入口
- `/wh/item-inventory-bill-batch/page`
- `getItemInventoryBillBatchByBatchNo(...)`

## 真实行为
- 提供基础 CRUD、分页、导出和按 `batchNo` 反查。
- service 本身很薄，说明批次真正的业务语义在 `iteminventorybill` 主链里。
- 它的主要价值是把一次盘点单下拆分出来的“批次扫描轮次/批次编号”落成独立实体。

## 在主链中的作用
- 给单件盘点单承接批次粒度。
- 作为批次明细、批次统计和批次回查的锚点。

## 风险与待确认
- `⚠️` `deleteItemInventoryBillBatch(...)` 仍是物理删除。
- `⚠️` 这层本身没看到状态控制，完整生命周期完全依赖上层盘点单主链。
