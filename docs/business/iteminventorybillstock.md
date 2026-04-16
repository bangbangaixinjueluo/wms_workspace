# 单件盘点账面库存快照模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`iteminventorybillstock` 是盘点单建单时冻结的“账面库存备份池”。它本身很薄，但承担的是盘点基线，不是普通从表。

## 关键入口
- Service: [ItemInventoryBillStockServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/iteminventorybillstock/ItemInventoryBillStockServiceImpl.java)

## 真实行为
- `create/update/delete/get/page`
  - 服务层本身很薄，核心职责是持久化盘点时刻的账面库存快照。

## 在主链中的作用
- `iteminventorybill`
  - 盘点单建单时会冻结一份账面库存快照，后续差异分析以此为基线。

## 风险与待确认
1. `⚠️` 服务层只有薄 CRUD，真实建快照和删快照动作分散在主单业务层，后续排障不能只看这里。
2. `⚠️` `deleteItemInventoryBillStock(...)` 仍是物理删除，未见主单状态保护。
