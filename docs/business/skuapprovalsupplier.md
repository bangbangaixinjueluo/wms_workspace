# SKU 审批域供应商 BOM 缓存
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
这层不是正式 `supplier-sku` BOM，而是 `newsku / updatesku` 审批域里的供应商 BOM 缓存镜像。它把待生效或待改单的供应商头、辅石/辅料、半成品/配件结构提前挂到审批明细 `skuDetailId` 之下，供审批详情、批量修改、企微详情和最终生效链消费。

## 关键入口
- Service: [NewSkuSupplierServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuSupplierServiceImpl.java)
- Service: [NewSkuSupplierAccessoriesServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuSupplierAccessoriesServiceImpl.java)
- Service: [NewSkuSupplierPartServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuSupplierPartServiceImpl.java)
- Service: [UpdateSkuSupplierServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuSupplierServiceImpl.java)
- Service: [UpdateSkuSupplierAccessoriesServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuSupplierAccessoriesServiceImpl.java)
- Service: [UpdateSkuSupplierPartServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuSupplierPartServiceImpl.java)

## 真实行为
### 1. 这层是正式 BOM 的审批镜像，不是独立业务单
新建款侧缓存表：
- `wh_new_sku_supplier`
- `wh_new_sku_supplier_accessories`
- `wh_new_sku_supplier_part`

改款侧缓存表：
- `wh_update_sku_supplier`
- `wh_update_sku_supplier_accessories`
- `wh_update_sku_supplier_part`

字段结构和正式 `wh_supplier_sku / accessories / part` 高度镜像，说明它们的定位就是“审批期间的 BOM 镜像层”，不是独立主数据。

### 2. 供应商头表里已经带完整费用结构
无论新建款还是改款，supplier 头表都直接承载：
- `supplierId/supplierNo/supplierName/supplierCode`
- `costGramCmtFee/costItemCmtFee`
- `saleGramCmtFee/moreItemCmtFee`
- `costEnamelFee/saleEnamelFee`
- `ropeWeavingFee/saleRopeFee`
- `testingFee/saleTestingFee`
- `costStoneFee/saleStoneFee`
- `costOtherFee/saleOtherFee`
- `skuCostOtherFee/skuSaleOtherFee`
- `basicGramCmtFee/moreGramCmtFee`
- `isDirect`
- `bomSerialNo`

这说明审批期间“默认供应商费用投影到 SKU 主档”的输入，其实并不来自正式 BOM，而是先来自审批缓存 BOM。

### 3. `skuDetailId` 才是审批域里的真实归属键
这层最关键的不是 `skuNo`，而是：
- `skuDetailId`
- `skuSupplierId`

也就是：
1. supplier 缓存先归属于某条审批明细
2. accessories / part 再归属于某条 supplier 缓存行

新建款侧主要按 `skuDetailId` 查；改款侧除了 `skuDetailId`，还额外保留 `skuNo`，以便在正式款号维度上做差异查询和同步。

### 4. 默认供应商在审批域里也已先行确定
`listDefaultByDetailIds(...)` 在新建款和改款侧都直接按：
- `isDirect = CommonConstant.TRUE`

筛默认供应商。也就是说，审批期间就已经决定“哪条供应商 BOM 是默认值”，后续正式生效时不会重新计算默认行。

### 5. 改款侧比新建款侧多一个“按 SKUNo 查缓存”的口径
`UpdateSkuSupplierServiceImpl` 比新建款多出：
- `listBySkuNo(String skuNo)`
- `listBySkuNos(Set<String> skuNos)`

说明改款 BOM 缓存除了挂审批明细外，还会直接按正式款号参与查询。这和改款链“围绕正式 SKU 做差异宿主”的定位是对齐的。

### 6. accessories / part 服务都非常薄，说明业务驱动在宿主层
无论新建款还是改款，辅石/辅料与半成品/配件服务基本都只做：
- `listByDetailIds(...)`

没有校验、没有状态、没有独立删除/审批逻辑。这说明它们只是审批域 BOM 的卫星层，真正的新增、修改、生效、覆盖都由 `NewSkuBusiness / UpdateSkuBusiness` 宿主层控制。

## 风险与待确认
1. 审批缓存 BOM 和正式 BOM 结构高度镜像，后续字段调整如果只改一边，极容易造成审批前后费用或辅料结构漂移。
2. 默认供应商 `isDirect` 在审批期就已确定，必须确认正式生效时是否完全复用缓存结果，还是会再次重算。
3. 新建款 supplier 缓存主要靠 `skuDetailId` 取数，改款侧同时支持 `skuNo` 取数，两边查询粒度不完全一致，后续很容易出现口径差异。
4. accessories / part 服务都是纯薄服务，卫星表完整性和顺序依赖业务宿主层一次性写对，服务层本身没有兜底校验。

## 结论
SKU 审批域供应商 BOM 缓存应定位为“正式 BOM 的审批镜像层”。它不是独立业务模块，但对新建款、改款审批详情、默认供应商确定、费用投影和最终 BOM 生效都非常关键。
