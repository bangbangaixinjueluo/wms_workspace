# 改款明细
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`wh_update_sku_detail` 不是普通改单子表，而是改款审批域的明细宿主。它在正式 `sku` 主档之外保存本次改款的差异快照、当前阶段、起始阶段、同步状态、上下架原因等执行信息；`wh_update_sku_log` 负责沉淀审批和操作轨迹。

## 关键入口
- Service: [UpdateSkuDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuDetailServiceImpl.java)
- Log Service: [UpdateSkuLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuLogServiceImpl.java)
- Entity: [UpdateSkuDetail.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/updatesku/UpdateSkuDetail.java)
- Entity: [UpdateSkuLog.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/updatesku/UpdateSkuLog.java)

## 真实行为
### 1. 改款明细是“正式款式 + 本次差异”的执行快照
`UpdateSkuDetail` 上不仅有和新建款类似的大量商品字段，还有改款专属执行字段：
- `billId/billNo`
- `state`
- `startState`
- `saveSaleFeeCount`
- `syncTime`
- `shutdownType/shutdownReason/shutdownOtherReason`
- `shelvesReason`

这说明改款明细不是“临时改个字段”，而是一条带阶段、带起点、带同步、带上下架原因的改款执行快照。

### 2. 改款明细服务承接“未同步明细池”口径
`listNotSynchronizedDetailsBySkuNos(...)` 会按：
- `skuNos`
- `startState`
- `state not in (SYNCHRONIZED, DISCARD)`

筛出还没同步的改款明细，而且会根据 `updating` 参数切换设计类状态集或更新/上下架状态集。这个口径已经明显不是通用 CRUD，而是改款执行链里的“待同步池”。

### 3. 列表页筛选还内置“按改款类型切状态”
`buildCondition(...)` 里通过 `filterUpdateType(...)` 把 `updateType` 解释成不同状态起点：
- 设计元素修改
- 下架原因修改
- 上架原因修改
- 其余普通改款

也就是说，前端传的“改款类型”最终不是查某个字段，而是映射成对 `startState` 的条件过滤。

### 4. 改款明细同样是企微和批次聚合的真实源头
和新建款一样，企微视图最终还是围绕改款明细宿主做条件筛选和批次聚合。只是这里除了 `state` 外，还显式带出 `startState`，说明改款侧更强调“从哪个阶段发起的修改”。

### 5. 日志表也是事件轨迹表，不是单纯备注
`wh_update_sku_log` 和新建款日志结构几乎镜像：
- `updateSkuId`
- `operateBatch`
- `state`
- `processUserId`
- `operator/operatorUser/operatorName`
- `operateTime`
- `operateType`
- `operateRemark`

因此改款的审批历史、已审/已驳回企微视图，本质上也是“日志反查明细”的双表链。

## 风险与待确认
1. `wh_update_sku_detail` 既承载商品快照又承载阶段执行信息，字段耦合度很高，后续字段增减要同时照顾正式主档和改款阶段语义。
2. `listNotSynchronizedDetailsBySkuNos(...)` 依赖多组状态枚举硬编码拼接，状态口径改动时很容易漏同步池逻辑。
3. `filterUpdateType(...)` 把业务“改款类型”直接映射成 `startState` 条件，这种隐式关系后续很容易被前端或新同事误解。
4. `UpdateSkuLogServiceImpl` 也是纯薄服务，日志是否完整完全依赖业务宿主层调用一致性。

## 结论
改款明细应定位为“改款审批域的差异快照宿主”，改款日志则是它的事件轨迹表。未同步池、企微待办、批次聚合、按改款类型筛选，本质上都建立在这对表之上。
