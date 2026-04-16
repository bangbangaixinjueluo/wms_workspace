# 盈亏调整单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`profitlossadjustdetail` 是盈亏调整链的事实层，承接成品或原料的行级调整数据，并负责给原料库存链计算 materialNo 维度差额。

## 关键入口
- Service: [ProfitLossAdjustDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/profitlossadjustdetail/ProfitLossAdjustDetailServiceImpl.java)

## 真实行为
- `fillList(respList)`
  - 批量补 SKU、品类、大类、材质、原料编号和名称。
- `computeForStock(details)`
  - 按 `materialNo` 汇总金重差额，供原料库存盈亏调整使用。
- `deleteByBillId(billId)`
  - 按单头整批物理删除。

## 在主链中的作用
- `profitlossadjustbill`
  - 主单决定走成品还是原料链；明细层提供行级事实和原料库存差额汇总。

## 风险与待确认
1. `⚠️` `fillData(...)` 为空，单条详情与批量列表的补数能力不一致。
2. `⚠️` `deleteByBillId(...)` 是物理删除，未见主单状态保护。
3. `⚠️` 原料库存差额完全依赖明细里的 `materialNo/goldWeight`，若建单明细口径错，库存链会直接偏差。
