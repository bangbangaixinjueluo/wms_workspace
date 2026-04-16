# 客户订单看板

## 模块定位
- 入口：`CustomerOrderKanbanController`、`CustomerOrderBusiness`
- 依赖宿主：
  - `wh_customer_order`
  - `wh_customer_order_detail`
  - `wh_customer_order_item`
- 这不是独立业务单，而是客户订单域的聚合看板层，提供“订单日报汇总 + 款式维度日报”两类视图。

## 主要查询入口
- `/wh/customer-order-kanban/order`
  - 返回 `CustomerOrderKanbanOrderVO`
  - 聚合“当天新增/累计订单”等订单级指标
- `/wh/customer-order-kanban/sku`
  - 返回 `CustomerOrderKanbanSkuVO`
  - 聚合“款式/模号/规格”维度的下单、发货、累计发货数据

## 订单汇总口径
- `getKanbanOrder(pageVO)` 主要取两组值：
  - `customerOrderService.getKanbanCount(reportDate)`
  - `customerOrderService.getKanbanTotal()`
- 说明这层把“日报新增量”和“全量累计量”拆开看，不是单一时间窗统计。

## 款式日报口径
- `getKanbanSku(pageVO)` 的核心链路是：
  - 先取某天建单得到的 `orderIdByDate`
  - 再查当日 `customer_order_detail` 聚合
  - 再查全量 `customer_order_detail` 聚合
  - 再查当日 `customer_order_item` 聚合
  - 再查全量 `customer_order_item` 聚合
- 最终按 `skuNo + modelNo + specs` 组合成唯一键。
- 所以它展示的不是单一表快照，而是“订单计划量 + 单件实际发货量”的双层聚合视图。

## 查询语义
- `detail` 侧更偏“订单计划/下单量”。
- `item` 侧更偏“单件执行/发货量”。
- 看板页把这两层拼在一起后，才能得到：
  - 当日下单数
  - 累计下单数
  - 当日发货数/金重
  - 累计发货数/金重

## 关联模块
- `customerorder`：订单主宿主。
- `customerorderreceive`：接单层来源。
- `deliverybill`：发货结果的下游来源之一。
- `customerorderitem`：发货统计直接依赖的单件执行层。

## 关键结论
- 客户订单看板不是执行链，而是客户订单域的 BI 聚合层。
- 它最重要的特点是同时拼接“订单明细层”和“单件执行层”，因此看到的发货数据不只是订单状态，而是实际单件结果。

## 风险与疑点
- `⚠️` 看板按 `skuNo + modelNo + specs` 聚合，若同款同模同规格跨其他业务维度仍需区分，这里会天然折叠。
- `⚠️` 订单汇总和款式日报混用了“日报窗口 + 全量累计”两套口径，前端展示如果不标清会容易误读。
