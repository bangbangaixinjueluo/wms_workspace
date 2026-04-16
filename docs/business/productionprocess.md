# 生产工艺
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
生产工艺不是普通下拉选项，而是 SKU、新建款、改款、导入分析和设计元素批量修改共同依赖的工艺规则主数据。它承载工艺编码、工艺名称、状态、排序和部门归属，并参与导入校验、工艺名称格式化和正式 SKU 同步。

## 关键入口
- Controller: [ProductionProcessController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/productionprocess/ProductionProcessController.java)
- Service: [ProductionProcessServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/productionprocess/ProductionProcessServiceImpl.java)
- Entity: [ProductionProcessDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/productionprocess/ProductionProcessDO.java)

## 真实行为
### 1. 工艺编码也是系统生成，不是自由录入
`create(...)` 调 `genProcessNo(...)`：
- 取当前最大 `processNo`
- 自动递增
- 格式化为 2 位编码
- 若撞号则递归继续加一

因此工艺编码不是用户自由规则，而是系统顺序编码。

### 2. 真正的唯一约束是工艺名称
`validProcessName(...)` 会校验：
- `processName` 全局唯一
- 更新时排除自身

而导入时则同时检查：
- `processName`
- `processNo`

说明平时维护主要以名称为唯一约束，导入场景才会同时兜住编码冲突。

### 3. 启停是状态切换，不是删除
`enable(id, status)` 会：
- 先校验记录存在
- 再 CAS 风格按当前状态更新目标状态

也就是说，生产工艺更偏可启停主数据，而不是经常物理删除的资料表。

### 4. 导入会自动补编码并默认启用
`importExcel(...)` 的行为是：
- 读 Excel
- 先查重已有名称/编码
- 收集错误信息
- 对空编码行自动补新编码
- 新导入行默认 `status = ENABLE`
- 最后批量插入

这说明导入链已经是“半自动主数据建档”模式，不要求每条都先手工生成工艺编码。

### 5. 这张表是 SKU 审批域工艺格式化和校验源
在改款设计元素批量修改里，业务会：
- 收集 `productionProcessNos`
- 用 `productionProcessService.getMapByNos(...)` 回查工艺
- 再把工艺编号格式化成工艺名称字符串

所以生产工艺不只是选项源，它还参与改款批量修改时的正式业务解释链。

## 风险与待确认
1. `processNo` 只有 2 位格式，后续工艺数量增长后需要确认是否足够。
2. `genProcessNo(...)` 递归找可用编码，若历史脏数据较多，编码生成和导入性能需要关注。
3. 导入时名称和编码重复会记错误并跳过，但当前没看到更细的批次级回显机制，需确认前端如何消费错误列表。
4. 当前只看到启停和导入，没看到删除保护；需确认是否允许被 SKU 历史引用的工艺被修改名称或停用。

## 结论
生产工艺应定位为“SKU 工艺规则主数据”。它既是选项表，也是导入分析、批量修改和正式 SKU 字段格式化的基础映射源。
