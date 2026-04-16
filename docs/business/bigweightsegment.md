# 大类重量段模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`bigweightsegment` 不是普通字典，而是按大类/大类类型管理重量段的规则主数据，模号和部分 SKU 逻辑会依赖它。

## 关键入口
- Service: [BigWeightSegmentServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/bigweightsegment/BigWeightSegmentServiceImpl.java)

## 真实行为
- `createBigWeightSegment(...)`
  - 建档时校验“同大类类型 + segmentNo”唯一。
- `getAllBigWeightSegment(goldWeight, skuId)`
  - 按 SKU 所属大类类型和金重匹配重量段，并按区间宽度从小到大排序。
- `getSegmentByBigType(s)` / `getSegmentByBigTypes(...)`
  - 支持按大类类型回查重量段。

## 风险与待确认
1. `⚠️` 删除仍是物理删除。
2. `⚠️` `getListByStatus(String s)` 实际按 `bigType` 查，不是按状态查，方法名和真实语义不一致。
