# 新建款明细
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`wh_new_sku_detail` 不是普通子表，而是新建款审批域的明细宿主。它把一条待建款 SKU 的基础属性、费用、图片、工艺、批次、审批状态、同步状态、申请人、审批人和驳回原因都沉到同一行里；`wh_new_sku_log` 则负责给这条明细补操作轨迹。

## 关键入口
- Service: [NewSkuDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuDetailServiceImpl.java)
- Log Service: [NewSkuLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuLogServiceImpl.java)
- Entity: [NewSkuDetail.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/newsku/NewSkuDetail.java)
- Entity: [NewSkuLog.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/newsku/NewSkuLog.java)

## 真实行为
### 1. 明细行本身就是一条“待生效 SKU 快照”
`NewSkuDetail` 上直接承载了大量主档字段，而不是只存外键，例如：
- `skuNo/skuName`
- `supplierId/supplierCode`
- `bigClassId/textureId/categoryId`
- `productDesc/productionProcess/factorySkuNo/mallSkuNo`
- `sampleGramWeight/minGramWeight/maxGramWeight`
- `specs/goodsSkuNo/single/autoGen`
- `cost.../sale.../grossProfit/gramProfit/itemProfit`
- `imageUrl/imageUrl2/imageUrl3`
- `drawingNo/designImageName/modelNo`

这说明新建款审批时，并不是“主档散落在多表上实时拼出来”，而是先把大部分待生效信息固化在明细宿主上。

### 2. 明细服务不是薄分页，而是审批域主查询入口
`NewSkuDetailServiceImpl.pageDetail(...)` 的查询条件非常重，覆盖：
- 状态、批次、创建人、申请时间、审批时间、同步时间
- 款号、款名、供应商、品类、大类、材质、产线、工艺
- 图片是否存在
- 多组成本/销售费用过滤
- 营销名、研发模式、别名标签
- 样版克重条件表达式

这说明审批列表页、检索页看到的不是“单头单条件查询”，而是直接对明细宿主做多维业务筛选。

### 3. 企微待办页也是直接读明细宿主
`qywxList(...)` 直接基于 `wh_new_sku_detail` 按这些字段筛选：
- `supplierIds`
- `states`
- `latestOperateBatch`
- `submitTime`

再由 `NewSkuBusiness` 按 `latestOperateBatch` 聚合同批明细。也就是说，企微页本质上不是读日志主表，而是“明细状态 + 批次聚合”的二次视图。

### 4. 日志表是批次审批轨迹，而不是明细补充字段
`wh_new_sku_log` 的关键字段包括：
- `newSkuId`
- `operateBatch`
- `state`
- `processUserId`
- `operator/operatorUser/operatorName`
- `operateTime`
- `operateType`
- `operateRemark`

它更像“审批轨迹事件表”。新建款的已审/已驳回企微视图，实际上就是先读日志，再反查明细。

### 5. 明细宿主的状态字段已经具备完整执行语义
`NewSkuDetail` 自带：
- `state`
- `latestOperateBatch`
- `processUserId`
- `submitTime`
- `approvedMan/approvedTime`
- `syncTime`
- `rejectReason`

因此它不是静态缓存，而是一条真正会经历“保存、提交、审批、同步、驳回”的执行态明细。

## 风险与待确认
1. `wh_new_sku_detail` 承载字段过多，已经接近“待生效 SKU 快照表”，后续字段演进如果不同步缓存表，很容易和正式 `sku` 主档漂移。
2. 企微待办页直接读明细宿主再按批次聚合，若 `latestOperateBatch` 生成或回写异常，会直接影响待办分组。
3. `NewSkuLogServiceImpl` 是纯薄服务，日志完整性完全依赖 `NewSkuBusiness` 调用方自觉落轨迹。
4. 明细查询条件里混入大量费用、图片、营销标签、工艺字段，后续索引和性能口径需要确认。

## 结论
新建款明细应定位为“新建款审批域的明细宿主 + 快照主表”，而新建款日志是它的批次轨迹表。列表、企微、审批历史、本轮批次视图都建立在这对宿主表之上。
