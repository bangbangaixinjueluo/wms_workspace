# 展销会

## 模块定位
- 入口：`ExhibitionController`、`ExhibitionServiceImpl`
- 宿主表：`wh_exhibition`
- 这不是纯资料表，而是“展销会主档 + 客户归属快照”的业务主数据。

## 关键入口
- `/wh/exhibition/create`
- `/wh/exhibition/update`
- `/wh/exhibition/page`
- `/wh/exhibition/export-excel`

## 真实行为
- 建单/改单都会调用 `setCustomerInfo(...)`，按客户带出：
  - `businessStaffId`
  - `businessAreaId`
  - `businessManageId`
- 这说明展销会单头会复制客户当前归属，而不是运行时实时跟随客户。
- 列表、详情、导出都走 `ExhibitionRespVO`，但 `fillData(...)` 目前为空，真实补数主要放在导出公共层 `excelCommonService.fillNameByIds(...)`。

## 在主链中的作用
- 承接展销会活动本体。
- 固化活动发生时的客户归属信息，给后续统计与导出使用。

## 关键结论
- 展销会表里已经带“客户归属快照”语义，不只是活动资料。

## 风险与待确认
- `⚠️` `deleteExhibition(...)` 仍是物理删除。
- `⚠️` 客户归属信息是在建单/改单时复制的，若客户后续换区换人，这里不会自动回刷。
- `⚠️` `fillData(...)` 为空，很多展示补数依赖外围导出或前端层，职责边界较散。
