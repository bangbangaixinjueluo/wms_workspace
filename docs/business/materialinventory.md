# 原料盘点单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`materialinventory` 是原料称重盘点单头宿主。它本身较薄，但承担的是原料盘点的主单职责，而不是普通资料表。

## 关键入口
- Service: [MaterialInventoryServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinventory/MaterialInventoryServiceImpl.java)

## 真实行为
- `create/update`
  - 建单/改单时会记录创建人或更新人姓名。
- `getMaterialInventoryByBillNo(billNo)`
  - 按单号回查原料盘点单头。

## 在主链中的作用
- `materialinventorydetail`
  - 明细层承接逐行事实；主单承接单头。

## 风险与待确认
1. `⚠️` 当前 service 只有薄 CRUD，真实盘点流程能力更多要到 controller 或业务层里找。
2. `⚠️` `deleteMaterialInventory(...)` 仍是物理删除。
