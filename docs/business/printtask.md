# 打印任务单

## 1. 模块定位

- 对应表：`wh_print_task`、`wh_print_task_detail`
- 入口：`PrintTaskController`、`PrintTaskServiceImpl`、`PrintTaskBusiness`
- 角色：标签打印执行宿主，承接单件打印任务、明细打印状态和打印次数

## 2. 主链结论

- 状态流：`NONE_PRINT -> PRINTING -> PRINTED`，也支持 `INFIRM` 回退和 `DISCARD`
- 支持按入库单 `entryBillId` 挂接
- 真正打印执行会继续下钻到明细层和单件打印状态

## 3. 核心职责

- 创建打印任务单头与明细
- 汇总 `totalNum/totalGoldWeight/totalWeight`
- 审核后把任务推到 `PRINTING`
- 完成后把任务推到 `PRINTED`
- 支持整单/单行重置打印状态

## 4. 代码结论

- 建单时使用 `BillTypeEnum.SINGLE_TAG_PRINT`
- `handleDetails(...)` 会按 `itemNo` 回查单件，并累计总件数与总重量
- `confirm(...)`：`NONE_PRINT -> PRINTING`
- `finishPrintTask(...)`：`PRINTING -> PRINTED`
- `resetTaskPrintStatus(...)` 会把单头重置到 `PRINTING`，同时把全部明细打印状态清回未打印

## 5. 关注点

- `⚠️ updatePrintTask(...)` 只有未打印状态允许修改，且依旧是删旧明细再重建
- `⚠️ confirm(...)` 里 `approvedTime` 使用的是单头当前值，不是显式重新赋值，需确认是否遗漏
- `⚠️ resetTaskPrintStatus(...)` 即使不是已打印也能重置，状态边界被放宽了
- `⚠️ getByEntryBillId` 查不到直接抛“明细为空”，错误语义不够准确
