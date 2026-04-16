# 款式供应商配件

## 模块定位
- 入口：`SupplierSkuPartServiceImpl`
- 宿主表：`wh_supplier_sku_part`
- 这是供应商 BOM 里的配件卫星层。

## 关键入口
- `deleteBySkuNos(Set<String> skuNos)`
- `listBySkuNos(Collection<String> skuNos)`
- `clearBySkuNo(String skuNo)`

## 真实行为
- 逻辑结构与 `supplierskuaccessories` 基本镜像。
- 核心围绕 `skuNo` 做批量查询、批量删除和单款清空。
- 作为供应商 BOM 的配件事实层，被上游款式宿主统一驱动。

## 在主链中的作用
- 给供应商款式 BOM 提供配件明细层。
- 与辅石层一起构成供应商 BOM 的卫星明细。

## 风险与待确认
- `⚠️` `deleteBySkuNos(...)` / `clearBySkuNo(...)` 都是物理删除。
- `⚠️` 当前没有独立状态与版本控制，完整性完全依赖上游 `suppliersku` 宿主层。
