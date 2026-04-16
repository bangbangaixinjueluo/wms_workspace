# 库位调整单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`shelvesbilldetail` 是库位调整链的 EPC 明细层，不只是从表。它承接被锁定和被迁移的单件清单。

## 关键入口
- Service: [ShelvesBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/shelvesbilldetail/ShelvesBillDetailServiceImpl.java)

## 真实行为
- `getVOListByBillId(billId)`
  - 按单头拉明细时，会用 EPC 回查单件，再补 `skuNo`。
- `getListByBillId(billId)`
  - 提供 Excel 导出所需明细清单。

## 在主链中的作用
- `shelvesbill`
  - 主单负责锁 EPC 和迁移库存；明细层承接被迁移的单件事实。

## 风险与待确认
1. `⚠️` `deleteShelvesBillDetail(...)` 仍是物理删除。
2. `⚠️` `getVOListByBillId(...)` 按 EPC 回查当前单件补 `skuNo`，历史明细展示可能受当前单件状态影响。
