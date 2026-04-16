# 部门折损分析报告

## 模块定位
- 入口：`DepartmentLossReportController`、`DepartmentLossReportServiceImpl`
- 宿主表：`wh_department_loss_report`
- 这不是执行单，而是部门折损分析结果宿主。

## 关键入口
- `/wh/department-loss-report/page`
- `/wh/department-loss-report/export-excel`
- `saveBatch(List<DepartmentLossReportDO>)`
- `getLossTotal(Collection<LocalDate>)`

## 真实行为
- service 负责保存折损分析结果和按条件查询。
- 查询支持两种分析维度：
  - `DATA_DIMENSIONS_DEPARTMENT`：按 `deptId` 聚合
  - `DATA_DIMENSIONS_SKU`：按 `skuId + skuNo` 聚合
- 若不走聚合维度，则按明细结果分页。
- `getLossTotal(dateList)` 会按 `reportDate` 维度汇总总损耗，说明这张表除了分页/导出，还承担日报级聚合来源。
- controller 同样通过 `SkuReportBusiness` 统一组装分页和导出。

## 在主链中的作用
- 承接部门折损分析离线结果。
- 给部门维度和 SKU 维度损耗分析提供结果层。

## 关键结论
- 它是报表结果宿主，不是损耗执行链本身。
- 其真实消费口径收敛在 `SkuReportBusiness`。

## 风险与待确认
- `⚠️` `getLossTotal(...)` 只按 `reportDate` 汇总，若后续还需要按部门/仓库分层汇总，现有结果层可能不够。
- `⚠️` 聚合维度同样是硬编码分支，新增分析维度时需要同步调整 service 和 business。
