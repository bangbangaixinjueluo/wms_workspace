# 客锁管理

## 模块定位
- 入口：`CustomerLockController`、`ItemBusiness`
- 宿主表不是单独的 `customer_lock`，而是单件主表 `wh_item` 上的客锁字段与单件日志。
- 这不是独立审批单，而是“客户锁定单件查询页 + 客锁释放入口”。

## 真实宿主
- 查询和释放都围绕 `wh_item` 运行。
- 关键字段是：
  - `customerLock`
  - `lockStatus`
  - 单件库存/仓库/库位字段
- 释放动作还会写 `wh_item_log`，`bizType = CUSTOMER_LOCK`。

## 主要业务动作
- `/page`
  - 走 `ItemBusiness.getCustomerLockPage(...)`。
  - 真实逻辑是给 `ItemPageReqVO` 强制加上 `filterCustomerLock = true`。
  - 同时按登录人仓库权限裁剪 `wareIds`。
- `/release/{id}`
  - 读取单件。
  - 校验 `customerLock != 0`。
  - 调 `itemService.releaseCustomerLocks(...)` 释放客锁。
  - 写一条“释放客锁”单件日志。
- `/batch-release`
  - 与单条释放相同，但批量执行并批量写日志。
- `/export`
  - 仍然基于客锁过滤后的单件视图导出，不是单独表导出。

## 查询口径
- `handlerReqVO(...)` 会统一做三件事：
  - 只看客锁数据。
  - 按当前登录人的仓库权限收缩查询范围。
  - 若前端传了 `marketingNames`，会先转成 `skuIds` 再查。
- 所以前端看到的是“带权限过滤的客锁单件池”，不是全量客锁台账。

## 日志链
- 释放后会落 `wh_item_log`：
  - `itemLogType = OTHER`
  - `bizType = CUSTOMER_LOCK`
  - `remark = 释放客锁/批量释放客锁`
- 这说明客锁释放不是静态字段清空，而是被当成单件业务事件记录。

## 关联模块
- `item`
  - 客锁真实承载体。
- `customerorder`
  - 客户订单挑货/锁货链会形成客锁来源。
- `customerorderreceive`
  - 前置接单与挑货流转会继续消费客锁单件池。
- `pack`
  - 部分订单执行链会读取单件锁定状态。

## 关键结论
- 客锁管理不是新表模块，而是单件域上的一个运营入口层。
- 它的核心业务意义不是“维护客锁资料”，而是让运营查询并释放被客户订单占住的单件。
- 所有客锁释放都应该同时看 `wh_item` 当前状态和 `wh_item_log` 事件痕迹。

## 风险与疑点
- `⚠️` 当前释放只校验 `customerLock != 0`，未见对订单状态、出货状态或其他占用链的进一步校验。
- `⚠️` 单条释放里既调 `createItemLog(...)`，又手工 `save(itemLog)`，与之前单件明细模块里的“可能重复日志”风险口径一致。
- `⚠️` 客锁模块没有独立宿主表，后续若误把它当主数据模块改造，很容易漏掉 `wh_item` 主链和日志链。
