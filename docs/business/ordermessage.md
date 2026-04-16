# 客户订单消息

## 模块定位
- 入口：`OrderMessageController`、`OrderMessageServiceImpl`
- 宿主表：`wh_order_message`
- 这不是普通通知表，而是客户订单域的“提醒宿主 + 业务员/经理待办宿主”。

## 关键入口
- `/wh/order-message/page`
- `/wh/order-message/read`
- `convertByCustomerOrderId(...)`
- `countTimeoutMessageByUserId(...)`
- `getAppMessagePage(...)`

## 真实行为
- 基础 CRUD 之外，最核心的是按订单生成消息和按当前登录人裁剪消息视图。
- `checkSalesManAndSetPageVO(...)` 会识别当前用户是不是业务分区经理或业务员；若是，只看自己的消息。
- `convertByCustomerOrderId(customerOrderId)` 会：
  - 读取客户订单
  - 读取客户当前业务员
  - 再取业务员所在分区领导用户集
  - 给“业务员本人 + 分区领导”各生成一条消息
- `read(id)` 会把消息切为已读并记录 `readTime`。
- `countTimeoutMessageByUserId(...)` 和 `getAppMessagePage(...)` 都只统计 `IMPORTANCE` 级别消息，说明这张表还承担 App 待办入口。
- `fillData(...)` 会补：
  - 业务员名
  - 接收人昵称
  - 大类名
  - 材质名

## 在主链中的作用
- 给客户订单超期、重要提醒、待处理事项提供统一消息宿主。
- 作为业务员和分区经理共用的消息待办池。

## 关键结论
- 这层不是通用消息中心，而是紧耦合客户订单域的提醒层。
- “谁该收到消息”并不是配置出来的，而是从客户当前业务归属实时推导的。

## 风险与待确认
- `⚠️` `deleteOrderMessage(...)` 仍是物理删除。
- `⚠️` `convertByCustomerOrderId(...)` 生成消息时取的是客户当前业务员/分区领导；若客户归属后来变化，历史消息接收口径可能和下单当时不一致。
- `⚠️` 重要消息统计只按 `degreeImportance = IMPORTANCE`，未额外限制未读状态，App 侧展示口径需要继续确认。
