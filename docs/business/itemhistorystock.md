# 单件历史库存

## 模块定位
- 入口：`ItemHistoryStockController`、`ItemHistoryStockServiceImpl`
- 宿主表：`wh_item_history_stock`、`wh_history_segment_stock`
- 这不是普通报表表，而是“单件每日库存快照 + 重量段汇总快照”双宿主。

## 关键入口
- `/wh/item-history-stock/page`
- `snapshot(LocalDate date)`
- `summaryBySegment(date, list)`

## 真实行为
- `snapshot(date)` 会从当前 `wh_item` 中抓取 `ON_WARE/ROUTING` 状态单件，生成指定日期的每日库存快照。
- 快照时会：
  - 把 `stockDate` 固化到目标日期
  - 计算 `stockDay`
  - 清空原 `createTime`
- 写入前会先删除当天旧快照，再整批插入。
- `summaryBySegment(...)` 会把当日单件按金重区间汇总到 `wh_history_segment_stock`，区间规则是硬编码的：
  - `<=10`
  - `10-12`
  - `12-15`
  - ...
  - `50-60`
  - `>60`
- 汇总同样是先删当天旧数据，再重建。

## 在主链中的作用
- 给历史库存查询提供每日快照宿主。
- 给重量段库存分析提供离线汇总表。

## 关键结论
- 这层不是运行时实时库存，而是“日切快照 + 分段汇总”的离线分析宿主。
- 重量段规则已经硬编码进 service，不是配置驱动。

## 风险与待确认
- `⚠️` `snapshot(...)` 和 `summaryBySegment(...)` 都采用先删后建，失败时会造成当天快照空窗。
- `⚠️` 重量段区间是硬编码的，若后续重量段规则调整，这里需要同步改。
- `⚠️` 快照只抓 `ON_WARE/ROUTING` 状态，是否完全符合历史库存口径要继续确认。
