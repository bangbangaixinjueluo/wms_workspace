# 供应商应付调整明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`supplieradjustbilldetail` 是供应商应付调整单的事实明细层。相比客户侧，它更薄一些，但依然承接了分页、导出和业务类型翻译。

## 关键入口
- Service: [SupplierAdjustBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/supplieradjustbilldetail/SupplierAdjustBillDetailServiceImpl.java)

## 真实行为
- `getPage(...)`
  - 走 mapper 自定义分页，并在结果里把 `bizType` 翻成业务文案。
- `getList(page/export)`
  - 分别承接列表和导出。
- `selectListByBillId(id)`
  - 主单回读整组明细。

## 在主链中的作用
- `supplieradjustbill`
  - 主单负责钱包审核链；明细层负责供应商应付调整的行级事实。

## 风险与待确认
1. `⚠️` `deleteSupplierAdjustBillDetail(...)` 当前物理删除，未见状态限制。
2. `⚠️` `getList(export)` 直接 convert 明细表结果，没有复用 `fillData(...)`，导出与分页的 `bizType` 展示口径可能不完全一致。

