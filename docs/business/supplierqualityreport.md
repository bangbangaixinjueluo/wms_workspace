# 供应商品质分析报告

## 模块定位
- 入口：`SupplierQualityReportController`、`SupplierQualityReportServiceImpl`
- 宿主表：`wh_supplier_quality_report`
- 这不是执行单，而是供应商品质分析结果宿主。

## 关键入口
- `/wh/supplier-quality-report/page`
- `/wh/supplier-quality-report/export-excel`
- `saveBatch(List<SupplierQualityReportDO>)`

## 真实行为
- service 本身负责保存分析结果和按条件查询分析结果。
- 查询支持两种分析维度：
  - `DATA_DIMENSIONS_SUPPLIER`：按 `supplierId` 聚合
  - `DATA_DIMENSIONS_SKU`：按 `skuId + skuNo + categoryId` 聚合
- 若不走聚合维度，则按明细结果分页。
- controller 实际不直接调用 service，而是转交 `SkuReportBusiness` 统一组装分页和导出视图。

## 在主链中的作用
- 承接供应商品质分析的离线结果。
- 作为 `SkuReportBusiness` 供应商品质报表的存储层。

## 关键结论
- 这层是“分析结果宿主”，不是运行时即时计算器。
- controller 和 service 之间还隔了一层 `SkuReportBusiness`，说明真实口径统一收敛在 BI 聚合链。

## 风险与待确认
- `⚠️` 报表控制层走的是 `SkuReportBusiness`，service 只是一部分结果存取层，后续不要误把它当成完整业务入口。
- `⚠️` 当前聚合维度是硬编码常量分支，若后续新增维度，service 和 business 要同步改。
