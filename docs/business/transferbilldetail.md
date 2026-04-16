# 单件调拨明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`transferbilldetail` 不是普通调拨子表，而是单件调拨链的“事实层 + 导出层 + 工序调拨报表层”。真正的调拨明细导出、工序调拨汇总和钻取都从这里出。

## 关键入口
- Service: [TransferBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/transferbilldetail/TransferBillDetailServiceImpl.java)

## 真实行为
### 1. 普通详情层
- `selectListByBillId(billId)`
  - 按调拨单头读取所有明细。
- `listTransform(reqVO)`
  - 直接走 mapper 自定义查询，供工序调拨视图使用。

### 2. 导出不是直接吐明细表
`exportTransferBillDetailExcel(...)` 会额外拼：
- `TransferBill.bizDate`
- `Item.categoryName`
- `bigClassName/textureName`
- 调出仓/调入仓名称
- 调出库位/调入库位名称

同时导出前还会按当前登录人的 `wareIds` 做仓库范围过滤。

### 3. 工序调拨报表宿主也在这里
- `procTransformNew(...)`
  - 返回分页记录 + 汇总行 `summary`
- `getexportGoodsTransformExcelNew(...)`
  - 先输出明细，再手工追加一条“总计”行

## 在主链中的作用
- `transferbill`
  - 主单负责状态流；明细层负责 EPC、仓位、品类、工序调拨报表事实。
- `item/warehouse/location/bigclass/texture`
  - 导出视图强依赖这些主数据回补展示字段。

## 当前结论
- 单件调拨明细已经不只是从表，而是调拨执行域的真实报表宿主。
- 想看“调了哪些货、从哪调到哪、工序调拨汇总是多少”，最终都得回这层。

## 风险与待确认
1. `⚠️` `deleteTransferBillDetail(...)` 当前物理删除，未见主单状态保护。
2. `⚠️` 导出依赖当前登录人的 `wareIds`，如果用户无仓库权限会直接导出空集。
3. `⚠️` `getexportGoodsTransformExcelNew(...)` 手工追加“总计”行，前端或下游若当普通明细消费会混淆。
4. `⚠️` `procTransformNew(...)` 在无数据时直接返回空 `PageResult`，没有 summary 兜底。

