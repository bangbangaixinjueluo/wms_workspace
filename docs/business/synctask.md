# 接口同步任务

## 模块定位
- 对应表：`wh_sync_task`
- 入口：`SyncTaskController`、`SyncTaskServiceImpl`
- 这不是简单的接口日志表，而是老庙/共享库存/档案同步链的“任务宿主 + 回调宿主 + 协议痕迹表”。

## 主数据职责
- 关键字段包括：
  - `taskNo`
  - `apiKey / applicationName`
  - `bizType / bizId / bizNo`
  - `outTaskId`
  - `requestMethod / requestUrl / requestParams`
  - `beginTime / endTime / duration`
  - `responseData / resultCode / resultMsg`
  - `tryCount`
  - `callbackTime / callbackStatus / callbackParams / callbackResult / callbackReply / callbackCount`
- 这说明它不仅记录“发请求”，还承接“回调处理结果”。

## 基础 CRUD
- 控制器提供标准创建、修改、删除、分页、导出。
- 另外还提供 `/get-by-bizNo`，但真实返回不是直接查表，而是委托 `CustomerOrderIntentBusiness.getTaskList(...)`。

## 任务创建链
- 除了普通后台创建，服务层还支持 `createSyncTask(SyncTaskDO)` 自动建任务：
  - 自动生成 `taskNo = 当前时间戳`
  - 用于真正的同步业务链落任务

## 回调宿主职责
- `updatePushDpListSyncTaskByCallback(form)`：
  - 按 `task_id -> taskNo` 反查同步任务
  - 记录 `callbackTime/callbackParams/callbackCount`
  - 逐条更新 `itemShare` 的同步结果
  - 汇总写回 `callbackStatus/callbackResult`
  - 某些业务类型下还会继续回写 `entryBill.syncStatus`
- `updateYxysSyncTaskByCallback(form)`：
  - 承接老庙意向预售单回调
  - 反查 `saleBill`、`customerOrder`
  - 根据回调结果推进客户订单和结算单同步状态
  - 回写本次任务的回调处理结果

## 业务边界
- 这张表不是单一模块专用，至少已覆盖：
  - 单品档案共享同步
  - 老庙意向预售/PP/订单回调
  - 共享库存或库存相关对接任务
- 所以 `bizType` 才是解释这张表的第一关键字段，不是 `apiKey`。

## 关键结论
- `synctask` 是对外接口链的任务宿主，不只是请求日志。
- 它同时承担“发起任务”和“消费回调”的双重职责。
- `callback*` 字段是真正有业务意义的，不是预留空字段。

## 风险与疑点
- `⚠️` `taskNo` 直接用时间戳字符串生成，极端并发下唯一性需要确认。
- `⚠️` `/get-by-bizNo` 名义上是查同步任务，实际走的是 `CustomerOrderIntentBusiness` 聚合口径，接口语义和数据来源并不直观。
- `⚠️` `updateYxysSyncTaskByCallback(...)` 里同时改同步任务、客户订单、结算单，属于高耦合回调处理链。
- `⚠️` 这张表和 `wh_lm_api_transfer` 在“接口痕迹”上有部分重叠，后续排障容易分散到两套宿主里。
