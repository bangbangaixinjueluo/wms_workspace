# 单件转料明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`recycledetail` 不是普通子表，而是单件转料链的 EPC 事实层。转料明细、源/目标仓库、扫码状态、ERP 确认状态和导出展示，最终都落在这里。

## 关键入口
- Service: [RecycleDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/recycledetail/RecycleDetailServiceImpl.java)

## 真实行为
- `getRecycleDetailPage(pageReqVO)`
  - 分页会补源/目标仓库和门店，并返回 summary。
- `exportRecycleDetailExcel(reqVO)`
  - 导出时会补仓店、扫码状态、ERP 确认状态等展示字段。
- `getRecycleDetailByBillId(id)`
  - 按单头拉明细时会补 SKU 图片、SKU 名和品类。
- `deleteRecycleDetailByBillNo(billNo)`
  - 按单号整批物理删除转料明细。

## 在主链中的作用
- `goodsrecyclebill`
  - 主单承接成品扣减与原料增加；明细层承接逐件转料事实。

## 风险与待确认
1. `⚠️` `deleteRecycleDetailByBillNo(...)` 当前是物理删除，未见主单状态保护。
2. `⚠️` 导出里 `isScan/erpConfirm` 都是运行时翻译，展示口径依赖当前枚举规则。
3. `⚠️` `getRecycleDetailByBillId(...)` 通过当前 SKU 主档补图和名称，历史展示可能受 SKU 主档变更影响。
