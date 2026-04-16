# 客户订单单件明细

## 模块定位
- 入口：`CustomerOrderItemServiceImpl`、`CustomerOrderItemMapper`
- 宿主表：`wh_customer_order_item`
- 这不是普通子表，而是客户订单履约域的“单件事实层 + 看板聚合来源层 + 余单日报来源层”。

## 关键入口
- `getCustomerOrderItemVOPage(...)`
- `getKanbanItem(...)`
- `updateSaleBillId(...)`
- `getUnMarkingEpcsByOrderNo(...)`
- `saveSurplusReport(...)`

## 真实行为
- 行级记录按 `orderNo/orderId + EPC` 承接客户订单实际占用到的单件。
- `fillData(...)` 会按当前 `EPC` 回查 `wh_item`，补齐：
  - `specs`
  - `skuName`
  - `categoryName`
  - `imageUrl`
- 若行上已挂 `saleBillId`，还会继续反查销售结算单号。
- `updateBatchByEpcAndOrderNo(...)` 会批量回写字印结果。
- `updateSaleBillId(...)` 会按订单号 + EPC 集合把履约单件挂到销售结算单。
- `deleteCustomerOrderByOrderId(...)` 只删除还没挂 `saleBillId` 的行，说明一旦已经进入销售链就不能随意清掉。
- `getKanbanItem(...)` 会按 `skuNo/modelNo/specs` 分组，为客户订单看板提供实际发货单件聚合。
- `saveSurplusReport(...)` / `getSurplusReportByDay(...)` 又把它接到了订单结余日报链。

## 在主链中的作用
- 承接客户订单从“订单需求”到“实际单件发货”的履约事实。
- 给看板、结余日报、字印、销售出货、客留等链路提供单件依据。

## 关键结论
- 这张表是客户订单域里最接近“真实履约 EPC 事实”的一层，不是薄卫星。
- 它同时被看板、销售、字印和日报消费，口径很容易被改串。

## 风险与待确认
- `⚠️` `fillData(...)` 大量依赖当前 `wh_item` 补数，历史履约明细展示会随单件主档变化漂移。
- `⚠️` `getKanbanItem(...)` 里 `skuNo` 过滤实际用了 `CustomerOrderItemDO::getModelNo`，这里看起来是明显的字段用错风险。
- `⚠️` `deleteCustomerOrderByOrderId(...)` 只保留“已挂销售单”的行，若后续还有其他下游引用，保护边界需要继续确认。
