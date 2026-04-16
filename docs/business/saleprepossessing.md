# 结算预处理

## 模块定位
- 入口：`SalePrepossessingController`、`SalePrepossessingServiceImpl`
- 宿主表：`wh_sale_prepossessing`
- 这不是普通资料表，而是客户订单发货前的结算预处理宿主。

## 关键入口
- `/wh/sale-prepossessing/create`
- `/wh/sale-prepossessing/update`
- `/wh/sale-prepossessing/confirm`
- `/wh/sale-prepossessing/infirm`
- `/wh/sale-prepossessing/discard`
- `/wh/sale-prepossessing/get-id-by-order-no`

## 真实行为
- 建单/改单时会把 `shipmentComeGoList`、`logisticsInsuranceList` 两组数组序列化成 JSON 字符串落表。
- 记录与 `customerOrderNo`、`markingNo` 强绑定，并会反查客户订单、字印、客户、业务员、大类、材质补展示字段。
- `/page` 实际会批量补：
  - 客户名
  - 业务员
  - 大类名、材质名
  - 挑货累计件数、金重
  - 字印类型和内容
- 该模块挂了 `@OrderMessageOperate` AOP，创建、修改、删除、审核、反审、作废都会触发订单消息侧链。
- 可按 `customerOrderNo` 反查最新一条预处理记录，也可反向拿到对应客户订单 ID。

## 状态流
- `INIT/INFIRM -> CONFIRM`
- `CONFIRM -> INFIRM`
- `INIT/INFIRM -> DISCARD`

## 在主链中的作用
- 承接客户订单进入销售结算前的预处理配置。
- 作为订单消息与结算前置动作之间的一条中间宿主链。

## 关键结论
- 这层不是正式销售结算单，而是“订单结算前准备层”。
- 它已经和客户订单、客户字印、订单消息三条链挂在一起。

## 风险与待确认
- `⚠️` `confirm/infirm/discard` 里的状态判断写法是 `Assert.isTrue(!状态匹配, ...)`，逻辑看起来像是反的，审核态校验需要重点确认。
- `⚠️` `deleteSalePrepossessing(...)` 仍是物理删除。
- `⚠️` 展示层大量依赖运行时补数，客户订单和字印主档变动会影响历史显示。
