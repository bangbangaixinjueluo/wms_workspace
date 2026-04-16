# 委外加工订单明细
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
委外加工订单明细不是独立业务单，而是委外主链的“明细查询与来源池模块”。它围绕 `wh_outsourcing_order_detail` 提供 5 类能力：
1. 明细分页与导出。
2. 来货/核销记录查询。
3. 单件入库待入库明细池。
4. 转成品入库来源池。
5. 工厂批量导出与采购汇总报表。

所以它的真实定位更接近“委外明细 BI + 下游来源服务层”，不是单独状态机。

## 关键入口
- Controller: [OutsourcingOrderDetailController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/outsourcingorderdetail/OutsourcingOrderDetailController.java)
- Service: [OutsourcingOrderDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/outsourcingorderdetail/OutsourcingOrderDetailServiceImpl.java)
- Mapper: [OutsourcingOrderDetailMapper.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/mysql/outsourcingorderdetail/OutsourcingOrderDetailMapper.java)
- SQL: [OutsourcingOrderDetailMapper.xml](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/resources/mapper/outsourcingorderdetail/OutsourcingOrderDetailMapper.xml)

## 依托数据
主表是 `wh_outsourcing_order_detail`，但查询时经常同时 join：
1. `wh_outsourcing_order`
2. `wh_demand_order`
3. `wh_demand_order_detail`
4. `wh_sku`
5. `wh_supplier`
6. `wh_texture`
7. `wh_big_class`
8. `wh_entry_bill_detail + wh_entry_bill`
9. `wh_order_writeoff_detail + wh_order_writeoff`

说明这个模块本质上就是围绕“委外明细 + 核销 + 入库 + 需求单”做的聚合视图。

## 真实能力
### 1. 明细分页查询
`getDetailQueryPage(...)` 调的是 `selectDetailQueryPage`，不是简单查主表。查询页会同时补齐：
1. 委外单头创建/审核时间、状态。
2. 款式、品类信息。
3. 需求单业务类型、业务员、客户/子客户。
4. 供应商、材质、大类。
5. `customerSkuNo` 这类需求单明细补充字段。

还带一些明显的业务口径筛选：
1. `totalBadNumCondition`
2. `totalNotReturnNumCondition`
3. `tabStatus = 1` 时只看仍有未回数量的明细
4. `enable = true` 时排除已作废委外单
5. `overdue = 0/1` 时按是否逾期分组

因此它是委外明细分析页，不是普通子表列表页。

### 2. 来货/核销记录
`getIncomingRecord(ddId, odId)` 实际查的是 `wh_order_writeoff_detail + wh_order_writeoff`，输出：
1. 合格量 `pass_num/pass_gram_weight`
2. 不合格量 `bad_writeoff_num/bad_writeoff_gram_weight`
3. 总核销量 `writeoff_num/writeoff_gram_weight`
4. 委外件数 `outsourcing_num`
5. 核销单号、审核人、来货时间、核销状态

这说明委外明细模块承担了“查看该委外行历史来货/核销轨迹”的职责。

### 3. 单件入库待入库池
`getWaitEntryDetail(orderId)` 会给某张委外单返回“尚可转单件入库”的明细，并额外算：
1. `can_entry_num = total_writeoff_num - 已入库数量`
2. `can_entry_gram_weight = total_writeoff_gram_weight - 已入库金重`

这里“已入库数量/金重”来自 `wh_entry_bill_detail + wh_entry_bill` 聚合，而且是按 `outsourcing_order_no` 反查。

所以它不是简单列明细，而是单件入库来源池的剩余额度视图。

### 4. 转成品入库来源池
`inboundOptions(...)` 是另一条来源池：
1. 只查 `convertInboundNo IS NULL`
2. 只查父单 `orderStatus = DELIVERED`
3. 支持按供应商、大类、材质、款号过滤

这条链对应的是“转成品入库可选明细池”。

### 5. 工厂导出
`factoryExportAll/factoryExportPart` 会把委外明细按供应商分组，导出成 Zip 内多个 Excel。导出时会额外拼出：
1. 供应商名
2. 图片
3. 材质要求
4. 客户拼接名
5. 款式工费
6. 工厂回复
7. 不合格件数/重量
8. 未回件数
9. 辅料备注/辅料名称
10. 二维码 `UL-WD$明细ID$`

说明它还是工厂侧打印/沟通的明细输出模块。

### 6. 采购汇总
`purchaseOrderSummary(...)` 不是只看委外明细表，而是 3 段 `UNION ALL`：
1. 委外下单量
2. 未回量
3. 订单核销量

最终按：
1. `supplier_id`
2. `biz_type`
3. `category_id`

汇总出：
1. 委外总量/预计总重
2. 核销总量/核销总重
3. 不合格总量/总重
4. 回厂总量/总重
5. 未回总量/总重

所以“采购订单汇总”其实是委外执行统计报表。

## 与主链的关系
### 1. 与委外单主链
它直接读取委外明细主表的累计字段：
1. `totalWriteoffNum`
2. `totalWriteoffGramWeight`
3. `totalBadNum`
4. `totalReturnNum`
5. `finishNum`
6. `convertInboundNo`
7. `overdueDays`

这些字段都不是它自己维护，而是由委外主链、订单核销、终止核销、入库链共同改写。

### 2. 与订单核销
`selectIncomingRecords` 直接以核销明细为来源，说明委外明细模块是“查看核销历史”的主要入口之一。

### 3. 与单件入库
`getWaitEntryDetail` 把“核销量 - 已入库量”转成来源池余额，属于单件入库前置查询模块。

### 4. 与转成品入库
`inboundOptions` 直接筛可转明细，属于成品入库前置选择模块。

## 风险与待确认
1. `deleteOutsourcingOrderDetail(Long id)` 是裸删明细，未见校验所属委外单状态，也未同步回滚需求单累计。
2. `getOutsourcingSaveNum(...)` 当前直接返回 `0`，像是未完成/废弃方法，需确认是否仍有调用方依赖。
3. `getWaitEntryDetail` 已入库数量按 `outsourcing_order_no` 聚合，不是按明细 ID；如果同单多行并行入库，可能出现跨行冲抵风险。
4. `purchaseOrderSummary(...)` 里“预计总重”用的是 `(min+max)/2 * outsourcing_num`，与委外主服务 `calcSumGramWeight(...)` 的 `* demandNum` 口径不一致。
5. 工厂导出里 `totalNotReturnNum = outsourcingNum - totalWriteoffNum`，没有再扣 `finishNum`，与汇总 SQL 的“未回量”口径不完全一致。

## 结论
委外加工订单明细应当被定位为“委外主链的分析与来源池模块”。它不推进单据状态，但承担了委外明细分析、核销历史查询、单件入库待入库池、转成品入库选择池、工厂导出和采购汇总这几条关键辅助链。后续只要改委外核销量、终止量、入库占用量或工厂导出口径，都必须把这个模块一起回归。
