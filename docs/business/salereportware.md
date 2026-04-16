# 销售统计按柜台

## 模块定位
- 入口：`SaleReportWareController`、`SaleReportWareServiceImpl`
- 宿主表：`wh_sale_report_ware`
- 这不是销售执行链，而是“按柜台/仓库/分区/客户/业务员”的销售统计报表壳。

## 关键入口
- `/wh/sale-report-ware/page`
- `/wh/sale-report-ware/pageSingle`
- `/wh/sale-report-ware/getPageByArea`
- 以及 service 里的：
  - `getSkuStockDailyByWare(...)`
  - `getSkuStockDailyByWareCategory(...)`
  - `getPageByArea(...)`
  - `getPageByCustomer(...)`
  - `getPageBySalesman(...)`
  - `...Single / ...Divide`

## 真实行为
- service 本身暴露了大量报表口径方法，说明这张表背后挂的是多条存储过程/聚合 SQL，不是单一列表。
- controller 里的 `/page` 会：
  - 组装日期参数
  - 按 `group` 决定走仓库口径还是仓库+品类口径
  - 再按当前登录人的 `wareIds` 做权限过滤
  - 调 `calList(list)` 补合计
  - 再把结果包装成 `shopNameSet/wareNameSet/pageResult`
- `/pageSingle` 是单件销售统计的镜像口径。
- 其余 `getPageByArea/getPageByCustomer/...` 说明它还承接了按业务分区、客户、业务员等多维统计。

## 在主链中的作用
- 承接销售统计类报表的结果层。
- 给仓库、品类、客户、业务员、分区等多维销售分析提供统一入口壳。

## 关键结论
- 这是“控制层很重、service 也不轻”的报表壳模块。
- 真正复杂度不在单表 CRUD，而在多维聚合、权限裁剪和结果重组。

## 风险与待确认
- `⚠️` controller 承担了大量真实口径处理，后续改报表不能只看 service。
- `⚠️` 这组接口大量返回 `Map + PageResult` 混合结构，前后端口径需要持续一致。
- `⚠️` `deleteSaleReportWare(...)` 仍是物理删除。
