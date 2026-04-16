# 单价维护明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`unitpricemaintaindetail` 是单价维护单的明细层，服务层较薄，但承接的是单价维护的行级事实。

## 关键入口
- Service: [UnitPriceMaintainDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/unitpricemaintaindetail/UnitPriceMaintainDetailServiceImpl.java)

## 真实行为
- `insertBatchDetails(detailDOS)`
  - 单价维护支持整批写入明细。
- `deleteUnitPriceMaintainDetailByBillNo(billNo)`
  - 按单号整批物理删除明细。

## 在主链中的作用
- `unitpricemaintain`
  - 主单承接维护流程，明细层承接逐行单价事实。

## 风险与待确认
1. `⚠️` `deleteUnitPriceMaintainDetailByBillNo(...)` 当前是物理删除，未见主单状态保护。
2. `⚠️` `fillData(...)` 为空，单条详情没有额外补数能力。
