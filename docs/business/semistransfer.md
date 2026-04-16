# 半成品调拨模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`semistransfer` 不是薄 CRUD，而是半成品调拨宿主链。它同时承接单头、明细、BOM、审批流转、收货/拒签和报表聚合。

## 关键入口
- Service: [SemisTransferServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/semistransfer/SemisTransferServiceImpl.java)

## 真实行为
- `save(transfer, detailList, bomList)`
  - 保存时会同时校验单号唯一、写单头、写明细、写 BOM。
- `modify(...)`
  - 修改采用“改单头 + 删旧明细/BOM + 全量重建”的模式。
- `confirm/infirm/discard/receipt/refusal`
  - 半成品调拨有完整状态流，不只是审核/反审。
- `getPage/getOptions/getExcelData/summary`
  - 报表与选单查询会直接联门店、仓库、供应商，并输出汇总。

## 在主链中的作用
- 半成品调拨链
  - 真实业务宿主，不是单纯卫星表。
- `goodsrejectbill`
  - 成品退厂和半成品调拨之间存在承接关系。

## 风险与待确认
1. `⚠️` `modify(...)` 采用删旧明细/BOM后重建，若中途失败会对历史挂接有较强破坏性。
2. `⚠️` 多个状态切换都依赖“按旧状态更新成功行数 > 0”的并发保护，调用方若状态判断错会直接失败。
3. `⚠️` `optionFilter(...)` 里 `filterRejected` 的语义是 `isNull(rejectBillNo)`，字段命名与实际筛选条件需要确认。
