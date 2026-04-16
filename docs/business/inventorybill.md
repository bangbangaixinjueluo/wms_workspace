# 盘点单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`inventorybill` 是旧版盘点单宿主，承接任务型盘点单头、审核/反审、盘盈盘亏汇总和跨日权限控制。

## 关键入口
- Service: [InventoryBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/inventorybill/InventoryBillServiceImpl.java)

## 真实行为
- `confirm(id)`
  - 审核前会按 `manner=2` 重新汇总盘盈盘亏件数和重量。
- `handleSum(inventoryBill)`
  - 通过“盘点 EPC 集合”和“仓库在库 EPC 集合”求交/求差来计算盘盈盘亏。
- `infirm(id)`
  - 反审会清掉盘盈盘亏汇总，并校验跨日权限 `PERMIT_INVENTORY_BILL_CROSS_DAYS`。
- `discard(id)`
  - 已审核单不能作废。

## 在主链中的作用
- `inventorytask`
  - 任务层承接盘点批次和扫描数据；主单承接总状态和总汇总。

## 风险与待确认
1. `⚠️` 盘盈盘亏汇总完全依赖当前“仓库在库 EPC”和任务 EPC 求差，历史时点口径较强依赖实时数据。
2. `⚠️` service 里没有正式库存过账，只是做汇总和状态流转。
