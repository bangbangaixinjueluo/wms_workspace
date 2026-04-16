# 单件入库单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`entrybilldetail` 不是普通子表，而是单件入库链的行级事实层。新生成单件的 `itemId/epc/itemNo`、入库行附件、导出行、汇总行，最终都落在这层。

## 关键入口
- Service: [EntryBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/entrybilldetail/EntryBillDetailServiceImpl.java)

## 真实行为
- `updateItemInfo(id, itemId, epc, itemNo)`
  - 审核生成单件后，把单件主键和 EPC 反写回明细，并把 `effective` 置为 `true`。
- `getEntryBillDetailPage(...)`
  - 2026-04-16 起，查询列表与页面导出统一补齐 `bigClassName`、`textureName`，用于大类 / 材质展示。
  - 分页时会补齐仓库、门店、供应商、配件明细，并返回 summary。
- `getEntryBillDetailList(pageReqVO)`
  - 导出前会在列表末尾手工追加一条 summary。
- `getExportDetailAccessories(...)`
  - 导出不是一行一明细；若存在配件，会把同一明细拆成多行输出。
  - 含辅料导出与普通页面导出保持同口径，同样输出 `bigClassName`、`textureName`。

## 在主链中的作用
- `entrybill`
  - 单件入库单审核时，先按明细生成 `wh_item`，再通过 `updateItemInfo(...)` 把新单件身份绑定回明细行。

## 风险与待确认
1. `⚠️` `deleteEntryBillDetail(...)` 当前是物理删除，未见主单状态保护。
2. `⚠️` 列表/导出都会手工追加 summary 行，前端和导出消费方需要区分“事实行”和“汇总行”。
3. `⚠️` 分页和列表路径里 `supplierCodeMap` 的构造口径不一致，一条按原字符串，一条按 `Integer.valueOf(...)+""`，可能造成供应商编码展示不一致。
4. `⚠️` 配件导出会把一条明细拆成多行，导出行数不再等于明细行数。
