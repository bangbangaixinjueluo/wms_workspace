# 款式供应商辅石

## 模块定位
- 入口：`SupplierSkuAccessoriesServiceImpl`
- 宿主表：`wh_supplier_sku_accessories`
- 这是供应商 BOM 里的辅石卫星层。

## 关键入口
- `deleteBySkuNos(Set<String> skuNos)`
- `listBySkuNos(Collection<String> skuNos)`
- `clearBySkuNo(String skuNo)`

## 真实行为
- 核心围绕 `skuNo` 批量查、批量删、按单个款号清空。
- 没有复杂校验和状态流，说明它是被 `suppliersku` / `skuapprovalsupplier` 这类宿主层驱动的卫星表。
- 这里承接的是“某款在某供应商 BOM 下挂了哪些辅石明细”。

## 在主链中的作用
- 给供应商款式 BOM 提供辅石明细层。
- 作为正式 `suppliersku` BOM 的一部分，被主链整体重建或整体清空。

## 风险与待确认
- `⚠️` `deleteBySkuNos(...)` / `clearBySkuNo(...)` 都是物理删除。
- `⚠️` 这层完全按 `skuNo` 清理，没有单独版本态或审批态保护，强依赖上游宿主层调用时机正确。
