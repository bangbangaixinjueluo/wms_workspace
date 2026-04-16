# 老庙回调接口

## 模块定位
- 入口：`LmCallbackController`
- 路径前缀：`/callback`
- 这不是内部业务模块，而是老庙向 TX 回传同步结果、发货结果、PP 结果的回调协议入口。

## 接口职责
- `/callback/gyswy/pushDplist`
  - 单品档案同步结果回传
  - 实际落点：`syncTaskService.updatePushDpListSyncTaskByCallback(form)`
- `/callback/pushYxysOrder`
  - 意向预售单状态回传
  - 当前真实处理：`deliveryBusiness.callbackYxysOrder(form)`
- `/callback/pushPPOrder`
  - PP 单状态回传
  - 当前真实处理：`deliveryBusiness.callbackPPOrder(form)`

## 主链特点
- 这组接口的职责是“老庙回调协议 -> 内部同步任务/发货业务”。
- 其中预售单和 PP 单原本看起来计划走 `syncTaskService.update*SyncTaskByCallback(...)`，但当前代码已经切到 `deliveryBusiness`。
- 说明这组回调入口的真实宿主已经从“同步任务表”转向“交付业务层”。

## 返回口径
- `pushDplist` 成功返回：
  - `status = 1`
  - `msg = 成功`
- `pushYxysOrder` / `pushPPOrder`：
  - 成功时直接返回 `deliveryBusiness` 组装好的 `LmApiResult<List<Map<String,Object>>>`
  - 失败时会按老庙协议逐条回填失败结果

## 权限与安全
- 三个接口同样都挂 `wh:callback:lm`
- 说明系统把老庙回调统一归到一条权限通道中。

## 关键结论
- `lmcallback` 是老庙回调协议适配层，不是同步任务宿主。
- 预售单和 PP 单回调的真实消费链已经偏向 `deliveryBusiness`，不再完全是 `syncTaskService`。
- 回调返回不是简单成功失败，而是按老庙格式逐条回填任务号、单号和处理消息。

## 风险与疑点
- `⚠️` `pushYxysOrder` / `pushPPOrder` 里原先走 `syncTaskService` 的代码已被注释，当前链路切换历史需要确认。
- `⚠️` 异常分支里构造失败列表时复用了同一个 `Map` 变量，循环内若不重新 new，可能造成多行结果串值风险。
- `⚠️` 这组接口同样没看到显式签名或来源校验逻辑，需要确认是否由网关承担。
- `⚠️` 控制层把业务异常翻译成老庙协议返回，但没看到统一的回调落痕主表，后续排障可能分散在 `syncTask` 和 `deliveryBusiness` 两边。
