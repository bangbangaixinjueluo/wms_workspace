# 单件作废单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`cancelbilldetail` 是单件作废链的行级事实层，不只是子表。它承接单件作废明细、来源单头补数、仓库/门店展示和图片补齐。

## 关键入口
- Service: [CancelBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/cancelbilldetail/CancelBillDetailServiceImpl.java)

## 真实行为
- `getCancelBillDetailPage(pageReqVO)`
  - 先按明细查询条件反查作废单头，再把单头状态、仓库、门店信息回填到明细页。
- `getCancelBillDetailListByBillId(id)`
  - 按单头拉明细时会补 SKU 图片。
- `getDetailListByBillIds(ids)`
  - 支持按多单头批量取作废明细并补图。

## 在主链中的作用
- `cancelbill`
  - 主单负责作废执行，明细层负责事实展示和来源单头补数。

## 风险与待确认
1. `⚠️` `deleteCancelBillDetail(...)` 仍是物理删除，未见主单状态保护。
2. `⚠️` 明细分页依赖先查单头再塞 `billIds`，若单头筛选逻辑变化，明细页可见性会一起漂移。
