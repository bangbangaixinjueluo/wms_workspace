# 即时库存报表

## 1. 模块定位

- 对应表：`wh_stock_report`、`wh_stock_report_detail`
- 入口：`StockReportController`、`StockReportServiceImpl`、`SkuStockDailyService`、`ReportAllService`
- 角色：即时库存与进销存汇总报表宿主

## 2. 主链结论

- `stockreport` 自身的 service 很薄，但真正业务宿主在 controller + `skuStockDailyService/reportAllService`
- 它不是库存结果表，而是“库存日报聚合 + 过滤 + 分组 + 明细钻取”的报表层

## 3. 核心职责

- 查询即时库存分页
- 提供按仓库名的库存查询
- 提供库存详情分页
- 提供进销存明细钻取
- 支持按仓库/网点分组汇总

## 4. 代码结论

- `/page` 的主体逻辑几乎都在 `StockReportController.getStockReportPage(...)`
- 实时库存来源主要是 `skuStockDailyService.getSkuStockDailyByOptions(...)`
- 明细钻取走 `reportAllService.stockReportDetails(...)`
- 分组口径支持 `ware_name`、`shop_name`

## 5. 关注点

- `⚠️ StockReportServiceImpl.validateStockReportExists(...)` 当前查不到直接 `return`，不存在时不会抛错
- `⚠️ getStockReportVOListByWareName(...)` 当前直接返回 `null`
- `⚠️ /page` 里 `bizDate == null` 的分支直接对空数组取下标，代码边界存在问题
- `⚠️` 这层真实宿主分散在 controller/service/report service，多处改动要一起回归
