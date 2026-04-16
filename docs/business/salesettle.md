# 销售结算明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`salesettle` 不是独立审核单，而是销售单的结算事实层。销售主单真正用了哪些结算方式、对应哪个材质、展示什么钱包类型，最终都落在这张明细表。

## 关键入口
- Service: [SaleSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/salesettle/SaleSettleServiceImpl.java)

## 真实行为
- `selectByBillId(id, isCheck)`
  - 按销售单头读取整组结算明细。
  - `isCheck = true` 时查不到会直接抛错。
- `batchInsertSaleSettle(...)`
  - 销售单建单/改单时整批落明细。
- `deleteByBillId(id)`
  - 销售单重建时按单头整批物理删明细。
- `fillData(...)`
  - 通过 `settlementService.getSettlement(settleId)` 补 `settleName/settleNo/settleType`
  - 通过 `materialService` 补 `materialName/walletTypeNo/materialId`

## 在主链中的作用
- `salebill`
  - 这是销售单的结算子表宿主，主单只承接整组明细。
- `settlement`
  - 结算方式文案和类型不是存死在本表，而是查询时回填。
- `material`
  - 材质钱包类型也在展示阶段回补，不是本表原生全量字段。

## 当前结论
- `salesettle` 属于“销售结算事实层”，不是独立单据流。
- 它最核心的价值是把销售链的结算方式和材质口径固定下来，再由主链消费。

## 风险与待确认
1. `⚠️` `deleteByBillId(...)` 当前是按单头整批物理删除，未见状态保护。
2. `⚠️` `fillData(...)` 是逐行查 `settlement/material`，批量详情页存在 N+1 风险。
3. `⚠️` `selectByBillId(..., true)` 查不到即抛错，调用方如果把“没有结算明细”视为合法场景会直接打断主链。

