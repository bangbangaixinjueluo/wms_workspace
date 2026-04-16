# 客户来料单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`materialinbounddetail` 是来料单的钱包/库存事实层，不只是原料明细子表。原料入库的重量、折算重量，以及后续钱包和库存两套计算口径，都是由这层汇总出来。

## 关键入口
- Service: [MaterialInboundDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinbounddetail/MaterialInboundDetailServiceImpl.java)

## 真实行为
- `getMaterialInboundDetailVOPage(...)`
  - 分页时会按业务员可见客户集过滤 `filterCustomIds`，并返回 summary。
- `getMaterialInboundDetailVOList(pageReqVO)`
  - 导出/列表会在末尾追加 summary。
- `computeForWallet(details)`
  - 按 `materialNo -> convertWeight` 汇总，供材质钱包侧使用。
- `computeForStock(details)`
  - 按 `materialNo -> originWeight` 汇总，供原料库存侧使用。
- `deleteDetailsByBillNo(...)`
  - 按单号整批物理删除原料明细。

## 在主链中的作用
- `materialinbound`
  - 主单审核/反审时，钱包用折算重量，库存用原始重量；这两套口径都直接来自明细层的计算结果。

## 风险与待确认
1. `⚠️` `deleteDetailsByBillNo(...)` 是物理删除，`deleteCount` 取到了但未校验。
2. `⚠️` 列表导出末尾追加 summary 行，消费侧要避开把汇总行当普通明细。
3. `⚠️` `fillData(...)` 当前是空实现，说明详情回显能力主要靠 mapper 输出，后续字段扩展容易两边漂移。
