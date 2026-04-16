# 原料盘点明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`materialinventorydetail` 是原料盘点链的明细层。服务层较薄，但承接的是原料称重盘点的逐行事实。

## 关键入口
- Service: [MaterialInventoryDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinventorydetail/MaterialInventoryDetailServiceImpl.java)

## 真实行为
- `getMaterialInventoryDetailByWalletTypeNo(walletTypeNo)`
  - 按钱包类型号取单条原料盘点明细。
- `getMaterialInventoryDetailByWareNo(wareNo)`
  - 按仓库编号取单条原料盘点明细。

## 在主链中的作用
- 原料盘点链
  - 承接原料盘点的逐行事实。

## 风险与待确认
1. `⚠️` service 层很薄，真实盘点冻结、差异和过账语义不在这层。
2. `⚠️` `deleteMaterialInventoryDetail(...)` 仍是物理删除。
3. `⚠️` `get...ByWalletTypeNo/get...ByWareNo` 都默认单条返回，若同口径存在多行明细，要确认是否足够。
