# 客户订单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`customerorderdetail` 不是普通子表，而是客户订单域的行级需求宿主。订单行的 SKU、示例金重、交期、出货状态和看板聚合，都由这层承接。

## 关键入口
- Service: [CustomerOrderDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/customerorderdetail/CustomerOrderDetailServiceImpl.java)

## 真实行为
- `fillList(...)`
  - 批量补 `skuNo/skuName/category/sampleGramWeight/imageUrl/deliveryCycle`，并计算 `sumGoldWeight`。
- `updateOutOrderStatus(id, outOrderStatus)`
  - 行级出货状态可以单独回写，不只是改单头。
- `deleteByOrderId(id)`
  - 按订单整批物理删除行明细。
- `getKanbanDetail(orderIds, skuNo, modelNo, specs)`
  - 看板不是查主单，而是按 `skuNo/modelNo/specs` 在明细层做 group by。

## 在主链中的作用
- `customerorder`
  - 客户订单单头承载整体状态，真实行需求、样重和看板聚合在明细层。
- `customerorderkanban`
  - 看板聚合依赖明细层的 grouped 结果。

## 风险与待确认
1. `⚠️` `deleteByOrderId(...)` 当前是物理删除，未见订单状态保护。
2. `⚠️` `getKanbanDetail(...)` 里 `skuNo` 过滤实际写到了 `modelNo` 条件上，字段口径需要确认。
3. `⚠️` `sumGoldWeight` 依赖 SKU 当前样重实时计算，历史订单明细展示可能受 SKU 主档变更影响。
