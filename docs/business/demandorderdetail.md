# 需求单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`demandorderdetail` 不是普通子表 CRUD，而是需求单域真正的“事实层 + 分页层 + 汇总层 + 委外关联展示层”。很多需求单看板、超期筛选和待到货统计，最终都落在这里做。

## 关键入口
- Service: [DemandOrderDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/demandorderdetail/DemandOrderDetailServiceImpl.java)

## 真实行为
### 1. 分页页直接拼接委外信息
`getDetailPage(...)` 会：
1. 以 `wh_demand_order_detail` 为主表。
2. 左联 `wh_demand_order`、`wh_sku`、`wh_outsourcing_order_detail`。
3. 用 `GROUP_CONCAT(...)` 把：
   - `finishReason`
   - `factoryAnswer`
   - `outsourcingOrderNo`
   - `outsourcingOrderLineNum`
   聚到同一行详情里。

### 2. 汇总不是简单 sum，而是带业务公式
同一分页会再跑一次 summary：
- `sumGramWeight = ((min + max) / 2) * demandNum`
- `notArrivedGramWeight = ((min + max) / 2) * GREATEST(demandNum - cancelNum - arrivedNum, 0)`
- 同时汇总 `producingNum/returnedNum/cancelNum/entryBillNum/transferNum/arrivedNum` 等累计字段。

### 3. 详情筛选承接了真实运营口径
`builderWrapper(...)` 除了基础条件，还承接：
- 逾期 / 未逾期
- tabStatus 待到货 / 已到货
- 业务员、客户、仓库权限
- 关键词跨 SKU、设计元素、镶石名称模糊检索

### 4. 删单是整单物理删明细
`deleteByOrderId(orderId)` 当前没有额外状态保护，直接按单头删整批明细。

## 在主链中的作用
- `demandorder`
  - 单头负责状态机；明细层负责累计量、超期、待到货和委外映射展示。
- `outsourcingorderdetail`
  - 分页页会直接聚合委外明细 finishReason / factoryAnswer / orderNo。
- `entrybill/goodstransferbill`
  - `entryBillNum/entryBillWeight/transferNum/transferWeight` 这些累计事实最终都回落在明细层。

## 当前结论
- 需求单明细是需求单域真正的查询宿主，而不是可忽略的从表。
- 想看“还差多少没到、已经委外到哪、已入库多少、已调拨多少”，都必须回到这张表。

## 风险与待确认
1. `⚠️` `deleteByOrderId(...)` 当前整批物理删除，未见对已审核或已承接链路的保护。
2. `⚠️` `GROUP_CONCAT(...)` 聚合委外单号、原因和工厂答复时没有显式排序，展示顺序可能漂。
3. `⚠️` `notArrivedGramWeight` 是按 `(min+max)/2` 的均值重量重算，不一定等于真实分批到货口径。
4. `⚠️` 关键词检索里设计元素和镶石名称都走正则/子查询，后续大数据量下性能需要重点关注。

