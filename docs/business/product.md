# 产品资料
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
产品资料不是执行单，而是商品主档入口。它把产品编号、品类、材质、产品类型、库存类标记、固定价和附加工费这些字段聚在一起，给商品选择下拉、导入、SKU/商品结构查询提供基础来源。

## 关键入口
- Controller: [ProductController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/product/ProductController.java)
- Service: [ProductServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/product/ProductServiceImpl.java)

## 真实行为
### 1. 建档当前最强约束是“产品编号唯一”
`createProduct(...)` 会显式校验：
- `productNo` 在未删除数据里唯一

通过后直接写入 `wh_product`。当前没看到对 `productName`、`productNameAux`、`categoryId`、`textureId` 的额外合法性校验。

### 2. 查询回显会补品类名和材质名
`fillData(ProductRespVO)` 会根据：
- `categoryId -> wh_category`
- `textureId -> wh_texture`

补出 `categoryName` 和 `textureName`。说明产品资料本体只存 ID，很多页面展示口径依赖运行时联表补全。

### 3. 简单列表是业务选择来源池
`getProductListByCategoryId(categoryId)` 支撑了按品类筛产品的简单列表接口，所以产品资料不是孤立主档，而是很多业务界面的商品来源池。

### 4. 导入链会自动做“按编号覆盖或新增”
`importDetail` 里实际行为是：
1. 逐行读取 Excel。
2. 如果 `productNo` 已存在，则转成 `ProductUpdateReqVO` 更新。
3. 如果不存在，则转成 `ProductCreateReqVO` 新增。
4. `categoryId` 字段实际按“品类编号”去查 `wh_category` 后再回填真实 `categoryId`。
5. `productType` 会通过字典 `product_type` 做转换。
6. 若 `storageType` 文案等于“库存类”，就被映射成 `"0"`。

所以产品导入不只是主表导入，还夹着品类编号解析和字典值转换。

### 5. 删除同样是裸删
`deleteProduct(id)` 只校验存在后直接物理删除 `wh_product`，没看到对：
- SKU
- 单据历史引用
- 商品选择来源池

做引用保护。

## 关键实体
- `wh_product`：产品资料主档。
- `wh_category`：产品所属品类。
- `wh_texture`：产品默认材质。
- 字典 `product_type`：导入和展示时的产品类型转换来源。

## 风险与待确认
1. `deleteProduct(...)` 没看到对 SKU、历史单据、商品来源池的删除保护。
2. `createProduct(...)` 没看到对 `categoryId/textureId` 是否存在的显式校验，可能留下悬空引用。
3. 导入接口里 Excel 字段 `categoryId` 实际上传的是“品类编号”，字段名和语义不一致，容易误导。
4. `storageType` 只把“库存类”文案映射成 `"0"`，其他文案没看到统一转换规则。

## 结论
产品资料应定位为“商品主档来源池”，不是纯字典。它直接挂接品类、材质和字典值，支撑导入、商品选择和后续业务界面的商品来源口径。
