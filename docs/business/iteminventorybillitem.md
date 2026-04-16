# 单件盘点单快照模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`iteminventorybillitem` 不是普通子表，而是盘点单的“单件快照池 + 差异池 + 统计宿主”。盘盈盘亏、盘点来源、差异页和统计页，都是从这张表计算出来。

## 关键入口
- Service: [ItemInventoryBillItemServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/iteminventorybillitem/ItemInventoryBillItemServiceImpl.java)

## 真实行为
- `getDifferencePage(ao)`
  - 差异页会按 `inventorySource` 派生 `profitStatus`，并返回 summary。
- `inventoryStatistics(ao)`
  - 把单件盘点、成品盘点和账面库存三套数据拼成黄金/白银/玉石三大统计视图。
- `exportDifferenceExcel(ao)`
  - 导出时会把 `inventorySource`、`itemStatus` 转成人类可读文本。
- `getDifferenceDataCount(ao)`
  - 直接返回差异数据条数。

## 在主链中的作用
- `iteminventorybill`
  - 主单建单时冻结快照；后续差异查询、统计分析和盘盈盘亏展示都靠这张快照表。

## 风险与待确认
1. `⚠️` `deleteItemInventoryBillItem(...)` 当前仍是物理删除，未见主单状态保护。
2. `⚠️` `profitStatus` 不是持久字段，而是按 `inventorySource` 运行时重算，查询口径需统一。
3. `⚠️` 统计逻辑按 `"01" / "04" / "07"` 硬编码拆黄金/白银/玉石，后续大类扩展要同步改代码。
