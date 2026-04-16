# 损耗单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`wastagedetail` 是损耗单的报表事实层，不只是从表。损耗单头、仓库、门店、SKU、材质、业务类型、审核状态和总计行，都会在这层汇总展示。

## 关键入口
- Service: [WastageDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/wastagedetail/WastageDetailServiceImpl.java)

## 真实行为
- `getWastageDetailVOPage(...)`
  - 分页时会补损耗单头、仓库、门店、SKU、材质，并返回 summary。
- `exportWastageDetailExcel(reqVO)`
  - 导出时会把业务类型、审核状态转成文本，并在末尾追加总计行。
- `getWastageDetailByBillNo(...)`
  - 支持按单号拉明细。

## 在主链中的作用
- `wastagebill`
  - 主单承接库存扣减；明细层承接报表与导出事实。

## 风险与待确认
1. `⚠️` 导出会手工追加总计行，消费侧要区分事实行和汇总行。
2. `⚠️` `fillData(...)` 当前为空，单条详情补数能力较弱。
3. `⚠️` `deleteWastageDetail(...)` 仍是物理删除，未见主单状态保护。
