# 客户/分区日均占料分析

## 模块定位
- 入口：`ReportCustomerAreaOweController`、`ReportCustomerAreaOweServiceImpl`
- 宿主表：`wh_report_customer_area_owe`
- 这不是执行单，而是“客户/业务分区日均占料分析”报表壳与结果宿主。

## 关键入口
- `/wh/report-customer-area-owe/page`
- `getProcCustomerOwe(map)`
- `getProcAreaOwe(map)`

## 真实行为
- 常规 service 提供基础 CRUD、分页和导出，但真正核心入口在 controller 的 `/page`。
- `/page` 不直接走普通分页，而是：
  - 组装开始/结束日期
  - 调用存储过程查询 `getProcCustomerOwe(...)` 或 `getProcAreaOwe(...)`
  - 再做角色权限过滤
  - 过滤“六个核心数值全为 0”的记录
  - 把 0 改成 `null` 以便前端展示
- 页面支持“按客户”和“按分区”两种口径。
- controller 里还保留了 `handleGroupByArea(...)` 这类二次聚合代码，说明这条分析链存在多层汇总口径。

## 在主链中的作用
- 承接客户和业务分区维度的日均占料分析展示。
- 给柜台/客户欠料类运营分析提供结果层。

## 关键结论
- 真实口径不在普通 mapper 分页，而在存储过程返回结果上。
- 这是“报表壳 + 存储过程 + 权限过滤 + 前端友好化”组合链。

## 风险与待确认
- `⚠️` `/page` 真实依赖存储过程结果，service 里的普通分页并不是主入口。
- `⚠️` controller 里过滤 0 并改 `null` 的逻辑会改变展示语义，后续前端/导出要统一口径。
- `⚠️` `handleGroupByArea(...)` 里 `mentalToday` 被连续 set 了两次，第二次本应是 `mentalAvg`，这里看起来有明显赋值错误风险。
