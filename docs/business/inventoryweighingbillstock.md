# 盘点称重单库存备份

## 模块定位
- 入口：`InventoryWeighingBillStockController`、`InventoryWeighingBillStockServiceImpl`
- 宿主表：`wh_inventory_weighing_bill_stock`
- 这是盘点称重单生成时的库存备份快照层。

## 关键入口
- `initInventoryWeighingBillStock(billId, wareId)`
- `/wh/inventory-weighing-bill-stock/page`

## 真实行为
- `initInventoryWeighingBillStock(...)` 会先按 `billId` 清空旧快照，再调用 mapper 从指定仓库重新初始化库存备份。
- 初始化时会带上当前登录人 ID，说明快照生成带操作者痕迹。
- 其余 CRUD 和分页导出都比较薄，核心价值在“盘点建单时冻结一版库存基线”。

## 在主链中的作用
- 给盘点称重单提供库存基线。
- 让后续称重差异计算有一份独立于实时库存的备份池。

## 风险与待确认
- `⚠️` `initInventoryWeighingBillStock(...)` 采用“先删后建”，若中途失败会留下空快照窗口。
- `⚠️` `deleteInventoryWeighingBillStock(...)` 仍是物理删除。
