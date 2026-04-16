# 打印任务明细

## 1. 模块定位

- 对应表：`wh_print_task_detail`
- 入口：`PrintTaskDetailController`、`PrintTaskDetailServiceImpl`
- 角色：打印任务 EPC/单件事实层

## 2. 核心职责

- 存储每个单件的打印状态、打印次数和总打印次数
- 支持按任务批量重置明细打印状态
- 打印成功后同步更新单件打印状态

## 3. 代码结论

- `resetDetailPrintStatus(...)` 和 `resetDetailPrintStatusByTaskId(...)` 都会把 `printStatus` 清成未打印、`printCount` 清零
- `updatePrintCount(...)`：
  - 单明细 `printCount + 1`
  - `totalPrintCount + 1`
  - 状态切成已打印
  - 同时调用 `itemService.updatePrintStatus(itemNo, printTemplate)`
- `getNonePrintCountByTaskId(...)` 提供任务剩余未打印数量

## 4. 关注点

- `⚠️ deletePrintTaskDetail(...)` 是物理删除，未见对单头状态保护
- `⚠️ resetDetailPrintStatusByTaskId(...)` 会把本次打印次数清零，但保留总打印次数，口径要注意
- `⚠️` 明细打印成功会继续改单件域，打印链不是纯本地子表
