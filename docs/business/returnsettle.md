# 退货结算明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`returnsettle` 是客户退货链的结算事实层。它和 `salesettle` 很像，但查询补数更重，直接承接退货页、退货详情页和导出口径。

## 关键入口
- Service: [ReturnSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/returnsettle/ReturnSettleServiceImpl.java)

## 真实行为
### 1. 列表页会按退货业务类型回填结算方式
`fillList(...)` 会：
- 收集 `settleNo/materialNo`
- 通过 `settlementService.getMapByNos(..., SETTLEMENT_RETURN)` 回补退货专用结算方式
- 通过 `materialService.getMaterialNoAndDoObjects(...)` 回补材质钱包类型

### 2. 建单和重建都是整批明细动作
- `batchInsertReturnSettle(...)`
  - 整批写入退货结算明细。
- `deleteSettlesByBillNo(billNo)`
  - 按退货单号整批物理删明细，且删不到会直接断言失败。

### 3. 既有列表补数，也有单条补数
- `fillData(List<...>)`
  - 批量回填材质与结算方式。
- `fillData(ReturnSettleRespVO)`
  - 单条详情回填时目前只补材质侧。

## 在主链中的作用
- `returnbill`
  - 退货主单承接审核和库存/钱包链；结算事实则挂在 `returnsettle`。
- `settlement`
  - 退货链明确按 `SETTLEMENT_RETURN` 业务类型取结算方式。
- `material`
  - 钱包类型和材质展示依赖材质主数据回补。

## 当前结论
- `returnsettle` 是退货链的真实结算事实层，不是简单子表。
- 相比 `salesettle`，它对“退货专用结算方式”的依赖更明确，展示层也更重。

## 风险与待确认
1. `⚠️` `deleteSettlesByBillNo(...)` 删不到直接断言失败，调用方如果遇到“无结算明细退货单”会被硬打断。
2. `⚠️` 单条 `fillData(resp)` 目前只补材质，不补 `settleName/settleType`，详情页和列表页口径不完全一致。
3. `⚠️` `fillData(List<...>)` 里直接取 `settlementDO.getId()`，若结算方式缺失可能空指针。

