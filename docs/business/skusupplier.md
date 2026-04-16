# SKU 供应商库
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`skusupplier` 不是正式 BOM，也不是审批缓存 BOM，而是“SKU 与供应商归属索引”模块。它把某个正式 SKU 和某个供应商建立一条轻量关系，并冗余保存工艺、工厂款号、表面工艺等摘要信息，供查询页和导出页使用。

## 关键入口
- Controller: [SkuSupplierController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/skusupplier/SkuSupplierController.java)
- Service: [SkuSupplierServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skusupplier/SkuSupplierServiceImpl.java)
- Entity: [SkuSupplierDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/skusupplier/SkuSupplierDO.java)

## 真实行为
### 1. 这层不是 BOM，字段更像供应商索引摘要
`wh_sku_supplier` 只保留：
- `supplierId`
- `skuId`
- `productionProcess`
- `factorySkuNo`
- `surfaceTechnics`
- `remark`

没有正式 BOM 那套费用、辅石、配件结构，所以它更像“某 SKU 归属于哪些供应商”的轻量索引表。

### 2. 建关系时会从正式 SKU 主档复制摘要字段
`createSkuSupplier(...)` 在循环写关系时，会先查正式 `SkuDO`，再把：
- `surfaceTechnics`
- `factorySkuNo`
- `productionProcess`

复制进 `wh_sku_supplier`。这说明这张表里的这些字段是从正式 SKU 冗余出来的，不是供应商关系独有字段。

### 3. 建关系支持一次挂多个 SKU
create 接口接收 `skuIds` 列表，会循环：
1. 校验每个 SKU 存在
2. 校验 `skuId + supplierId` 不重复
3. 逐条插入关系

所以它不是一次只建一条关系，而是支持把一组 SKU 批量挂到某个供应商名下。

### 4. 详情和分页页会继续补齐图片与名称
`fillData(...)` 会额外补：
- `supplierName`
- `skuNo/skuName`
- `imageFiles`
- `imageUrl`

其中图片来自：
- `fileApi.selectListByBizId(skuId, FILE_BIZ_TYPE_SKU)`

这说明这个模块本身不是只管关系，还承担了“供应商款式库查询视图”的组装职责。

### 5. 它和正式 BOM/审批缓存 BOM 是不同粒度
前面我们已经确认：
- `supplier-sku` 是正式 BOM 头
- `newsku/updatesku supplier` 是审批缓存 BOM

而 `skusupplier` 只有供应商归属和少量工艺摘要，没有 BOM 明细。它的定位更像“关系索引层”，不是费用结构层。

## 风险与待确认
1. `deleteSkuSupplier(...)` 当前是物理删除，没看到对查询、导出或历史引用的保护。
2. 关系建好后，若正式 `sku` 的 `productionProcess/factorySkuNo/surfaceTechnics` 再变化，这里的冗余字段是否同步刷新需要确认。
3. 当前重复校验只拦 `skuId + supplierId`，没看到对“同供应商下工厂款号重复”等更细业务约束。
4. 详情页图片直接按 `skuId` 拉 SKU 文件，说明这层自己没有独立图片归档，展示完全依赖正式 SKU 文件链。

## 结论
SKU 供应商库应定位为“SKU 与供应商的轻量归属索引层”。它不承担正式 BOM 费用结构，但负责把正式 SKU 的供应商归属、工艺摘要和图片视图组织起来，供查询和导出使用。
