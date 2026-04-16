# 维修入库单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`maintaininbilldetail` 是维修回收入库链的事实层，不是普通子表。维修单明细回收入库后的承接关系、最近一次回收结果、整批更新，都是通过这层维持。

## 关键入口
- Service: [MaintainInBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintaininbilldetail/MaintainInBillDetailServiceImpl.java)

## 真实行为
- `insertBatch(list)` / `updateBatch(list)`
  - 维修入库单建单、审核、反审都会整批写或整批更新。
- `findByMaintainBillDetailIds(ids)`
  - 只查询 `billStatus = "1"` 的已审核维修入库明细。
- `findLastMapByMaintainBillDetailIds(ids)`
  - 以 `id` 倒序取每条维修主明细最近一次维修入库结果。
- `removeByBillId(billId)`
  - 按单头整批物理删除维修入库明细。

## 在主链中的作用
- `maintaininbill`
  - 主单审核/反审时，维修单累计、单件返仓和“最近一次回收结果”都依赖这层。
- `maintainbilldetail`
  - 维修详情下推单件时，也会依赖“最近一次维修入库明细”的结果。

## 风险与待确认
1. `⚠️` `removeByBillId(...)` / `deleteMaintainInBillDetail(...)` 都是物理删除，未见主单状态保护。
2. `⚠️` “最近一次维修入库结果”是按明细自增 `id` 倒序推断，不是显式按业务时间认定。
3. `⚠️` `findByMaintainBillDetailIds(...)` 把已审核状态硬编码成 `"1"`，状态语义分散在字符串常量里。
