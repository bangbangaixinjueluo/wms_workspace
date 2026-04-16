# 库存销售周转率

## 模块定位
- 入口：`ReportTransferWarehouseController`、`ReportTransferWarehouseServiceImpl`
- 宿主表：`wh_report_transfer_warehouse`
- 这不是执行单，而是库存销售周转率报表壳与结果宿主。

## 关键入口
- `/wh/report-transfer-warehouse/page`
- `getPageByProc(map)`

## 真实行为
- service 提供基础 CRUD、分页、导出和 `getPageByProc(map)` 存储过程入口。
- controller 的 `/page` 才是真正主入口：
  - 默认统计区间是“本月第一天到今天”
  - 组装 `pStartDate/pEndDate`
  - 调存储过程 `getPageByProc(map)`
  - 再从结果里提取 `shopNameSet/wareNameSet`
  - 返回一个拼装好的 `Map`
- 说明这条链本质是“存储过程结果 + 控制层二次整理”的报表壳，不是普通分页表。

## 在主链中的作用
- 承接库存销售周转率分析结果。
- 给仓库/网点维度的周转率展示提供结果层。

## 关键结论
- 真正口径依赖存储过程，不在普通 `selectPage(...)`。
- controller 负责把结果加工成前端直接消费的结构。

## 风险与待确认
- `⚠️` `/page` 主入口依赖存储过程结果，service 普通分页并不是主口径。
- `⚠️` `deleteReportTransferWarehouse(...)` 仍是物理删除。
