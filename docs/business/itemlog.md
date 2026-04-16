# 单件日志模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`itemlog` 不是普通日志表，而是单件域的事件宿主。单件入库、出库、维修、销售、作废后的状态快照与轨迹，最终都落在这里。

## 关键入口
- Service: [ItemLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/itemlog/ItemLogServiceImpl.java)

## 真实行为
- `createItemLog(itemDO, billType, bizType, remark)`
  - 直接从当前 `wh_item` 快照生成一条日志，并默认把 `billStatus` 写成 `"0"`。
- `insertBatch(itemLogs)`
  - 多条执行链会整批写入单件日志。
- `getItemLogPage(...)`
  - 分页时补门店名和仓库名。
- `getItemLogByType(todayTime, yesterday, in)`
  - 查询指定时间窗和指定出入类型的日志；若日志里没写 `skuNo`，会回查 `wh_item` 补齐。
- `getItemLatestLog(itemIds)`
  - 取单件最新日志快照。

## 在主链中的作用
- `item`
  - 单件域的历史追溯、最近状态和出入方向主要依赖日志表。
- `entrybill / maintain / sale / shelves / discard`
  - 多条执行链都会在动作完成后同步补单件日志。

## 风险与待确认
1. `⚠️` `deleteItemLog(...)` 当前仍是物理删除，未见历史轨迹保护。
2. `⚠️` `getItemLogByType(...)` 会在日志缺 `skuNo` 时回查当前 `wh_item` 补齐，历史查询结果可能受当前单件状态影响。
3. `⚠️` `createItemLog(...)` 里默认把 `billStatus` 固定成 `"0"`，需要确认是否真的适用于所有日志来源。
