# 库存消息记录模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`stockmessagerecord` 不是普通日志，而是库存消息发送/消费状态的宿主。它只保留结果态更新能力，不承接复杂查询。

## 关键入口
- Service: [StockMessageRecordServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/stockmessagerecord/StockMessageRecordServiceImpl.java)

## 真实行为
- `updateSuccess(messageId)`
  - 把消息状态更新为处理成功。
- `updateFail(messageId, reason)`
  - 把消息状态更新为处理失败并记录失败原因。
- `updateSendFail(messageId, reason)`
  - 把消息状态更新为发送失败。

## 在主链中的作用
- 库存异步链
  - 主要承接库存消息投递/处理结果态。

## 风险与待确认
1. `⚠️` 这层只有状态更新入口，消息创建、重试和查询语义不在这里，排障不能只看本 service。
2. `⚠️` `messageId` 为空时只打日志不抛错，调用方可能误判更新成功。
