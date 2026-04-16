# 进销存汇总表明细

## 1. 模块定位

- 对应表：`wh_stock_report_detail`
- 入口：`StockReportDetailController`、`StockReportDetailServiceImpl`
- 角色：即时库存报表的明细查询与聚合卫星层

## 2. 核心职责

- 提供通用 CRUD、分页与导出
- 提供按仓库/品类、按重量/按件数的多种聚合查询
- 作为即时库存报表钻取层的 SQL 承载器

## 3. 代码结论

- service 主要价值在四个聚合方法：
  - `getSkuStockDailyByWare(...)`
  - `getSkuStockDailyByCategory(...)`
  - `getSkuStockDailyByWareNum(...)`
  - `getSkuStockDailyByCategoryNum(...)`
- `fillData(...)` 当前为空，说明报表展示主要依赖 mapper 查询结果

## 4. 关注点

- `⚠️ deleteStockReportDetail(...)` 是物理删除，但这张表更像报表事实层，删除保护需要确认
- `⚠️ fillData(...)` 为空，字段补数逻辑高度依赖 SQL 结果
