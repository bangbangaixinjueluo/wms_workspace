# 成品退厂单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`goodsrejectbilldetail` 是成品退厂单的报表事实层，不只是单据子表。退厂原因、工费、金重、件数和总计行，都是这层对外输出。

## 关键入口
- Service: [GoodsRejectBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsrejectbilldetail/GoodsRejectBillDetailServiceImpl.java)

## 真实行为
- `procGoodsInboundOutDetaiNew(...)`
  - 退厂明细分页会补业务类型和原因字典，并返回 summary。
- `getexportGoodsInboundBillDetailExcelNew(...)`
  - 导出时会在末尾追加总计行。
- `getListByBillIds(...)`
  - 支持按单头批量拉退厂明细。

## 在主链中的作用
- `goodsrejectbill`
  - 主单承接库存、钱包和来源挂接；明细层承接报表事实、原因回显和导出总计。

## 风险与待确认
1. `⚠️` 导出会手工追加总计行，消费侧要区分事实行和汇总行。
2. `⚠️` `deleteGoodsRejectBillDetail(...)` 当前仍是物理删除，未见主单状态保护。
3. `⚠️` 原因映射强依赖退厂原因字典，后续字典改造要同步检查报表层。
