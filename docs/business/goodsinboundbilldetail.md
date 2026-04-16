# 成品入库单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`goodsinboundbilldetail` 不是普通明细表，而是成品入库/退厂对照报表的事实层。入库原因、退厂原因、工费、金重、件数和总计行，都是从这层出。

## 关键入口
- Service: [GoodsInboundBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsinboundbilldetail/GoodsInboundBillDetailServiceImpl.java)

## 真实行为
- `procGoodsInboundInDetaiNew(...)`
  - 成品入库明细分页会补业务类型、原因名称，并返回 summary。
- `getexportGoodsInboundBillDetailExcelNew(...)`
  - 成品入库导出会在末尾追加总计行。
- `procGoodsInboundDetailNew(...)`
  - 混合“成品入库/退厂出库”对照报表，原因映射会根据 `type` 分流到不同字典。
- `exportProcGoodsInboundDetailNewExcel(...)`
  - 混合报表导出同样会追加总计行。
- `getDetailByBillIds(...)`
  - 按单头批量取明细，并按明细主键排序。

## 在主链中的作用
- `goodsinboundbill`
  - 主单承接库存和钱包动作；明细层承接事实报表、原因回显和导出汇总。

## 风险与待确认
1. `⚠️` 多条分页/导出路径都会手工追加总计行，消费侧要区分事实行与汇总行。
2. `⚠️` 混合报表里通过中文文案 `"成品入库"` 判断原因字典，后续若改文案会影响映射。
3. `⚠️` `deleteGoodsInboundBillDetail(...)` 仍是物理删除，未见主单状态保护。
