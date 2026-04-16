# 盘点称重明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`inventoryweighingdetail` 是盘点称重单的事实层，承接称重行明细、盘盈盘亏结果回填和称重对象补数。

## 关键入口
- Service: [InventoryWeighingDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/inventoryweighingdetail/InventoryWeighingDetailServiceImpl.java)

## 真实行为
- `fillList(respList)`
  - 批量补材质名、SKU 名、品类、大类、材质显示字段。
- `updateClearProfitLoss(billId)`
  - 审核/反审链会整批清零 `profitLossNum/profitLossGoldWeight`。
- `deleteByBillId(billId)`
  - 按单头整批物理删除称重明细。
- `insertBatch/updateBatch`
  - 称重明细支持整批保存与整批回写。

## 在主链中的作用
- `inventoryweighingbill`
  - 主单承接称重审核；真实称重对象和盈亏差值结果在明细层。

## 风险与待确认
1. `⚠️` `fillData(...)` 为空，单条详情与批量列表的补数能力不一致。
2. `⚠️` `deleteByBillId(...)` 当前是物理删除，未见主单状态保护。
3. `⚠️` `updateClearProfitLoss(...)` 会整单把盘盈盘亏清零，后续若需要保留历史差值痕迹要额外留链。
