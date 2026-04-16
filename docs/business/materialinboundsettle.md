# 来料结算明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`materialinboundsettle` 不是独立审核单，而是客户/供应商来料单的结算事实层。材质、结算方式和结算类型的补数都在这层。

## 关键入口
- Service: [MaterialInboundSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinboundsettle/MaterialInboundSettleServiceImpl.java)

## 真实行为
- `batchInsertMaterialInboundSettle(settleDOS)`
  - 来料单建单/改单时整批写入结算明细。
- `fillData(respList)`
  - 批量补材质、钱包类型、结算方式和结算类型。
- `getSettleRespByBillNo(billNo)`
  - 按单号拉来料结算明细并自动补数。
- `deleteSettlesByBillNo(billNo)`
  - 按单号整批物理删除结算明细。

## 在主链中的作用
- `materialinbound`
  - 来料主单的结算行事实层。

## 风险与待确认
1. `⚠️` `deleteSettlesByBillNo(...)` 当前没有校验删除结果。
2. `⚠️` service 里只有批量补数，没有单条 `fillData`，列表和单条查询口径不完全一致。
