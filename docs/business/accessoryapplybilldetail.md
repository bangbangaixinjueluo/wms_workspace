# 配件领用单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`accessoryapplybilldetail` 是配件领用单的行级明细层。服务层很薄，但承接的是配件领用事实，而不是普通后台资料。

## 关键入口
- Service: [AccessoryApplyBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/accessoryapplybilldetail/AccessoryApplyBillDetailServiceImpl.java)

## 真实行为
- `getAccessoryApplyBillDetailByBillNo(...)`
  - 按领用单号取单条明细。
- `getAccessoryApplyBillDetailByCategoryNo(...)`
  - 按配件分类号取单条明细。
- `getAccessoryApplyBillDetailByGoodsNo(...)`
  - 按配件货号取单条明细。

## 在主链中的作用
- `accessoryapplybill`
  - 领用单头承接流程，明细层承接行级配件事实。

## 风险与待确认
1. `⚠️` service 层只有薄 CRUD 和几条单字段查询，真实审核/库存语义分散在主业务层。
2. `⚠️` `deleteAccessoryApplyBillDetail(...)` 仍是物理删除。
3. `⚠️` `get...ByCategoryNo/get...ByGoodsNo` 都默认只取单条，若同类/同货号存在多行明细，要确认是否足够。
