# 调账单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`accountadjustbilldetail` 是调账单的双分录事实层。主单审核时做调出/调入双钱包动作，明细层则承接每一条账户调账分录。

## 关键入口
- Service: [AccountAdjustBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/accountadjustbilldetail/AccountAdjustBillDetailServiceImpl.java)

## 真实行为
- `insertBatch(details)` / `removeByBillId(id)`
  - 主单重建时整批重写明细。
- `getPage(...)`
  - 走 mapper 自定义分页。
- `getList(page/export)`
  - 承接列表与导出。
- `selectListByBillId(id)`
  - 主单回读整组明细。

## 在主链中的作用
- `accountadjustbill`
  - 主单负责双钱包审核链；明细层负责账户调入/调出的分录事实。

## 风险与待确认
1. `⚠️` `deleteAccountAdjustBillDetail(...)` 和 `removeByBillId(...)` 当前都是物理删除，未见状态保护。
2. `⚠️` 服务层未见展示补数逻辑，说明分页展示强依赖 mapper 侧 SQL，后续改字段要先找真实数据源。

