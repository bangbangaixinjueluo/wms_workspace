# SKU 规格
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`sku-specs` 不是独立执行单，而是 `sku` 主链下的规格卫星层。它承接圈口/长度、尺寸、重量上下限、客户定制重量口径等规格数据，既服务于生效后的 `wh_sku_specs`，也在新建款、改款审批过程中分别对应 `wh_new_sku_specs`、`wh_update_sku_specs` 两套临时规格池。

## 关键入口
- Controller: [SkuSpecsController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/skuspecs/SkuSpecsController.java)
- Service: [SkuSpecsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skuspecs/SkuSpecsServiceImpl.java)
- Cache Service: [NewSkuSpecsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuSpecsServiceImpl.java)
- Cache Service: [UpdateSkuSpecsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuSpecsServiceImpl.java)

## 真实行为
### 1. `wh_sku_specs` 是生效规格表，不是唯一规格来源
`SkuSpecsDO` 的核心字段包括：
- `skuNo`
- `circleMouth`
- `specSize`
- `minWeight/maxWeight`
- `isDirect`
- `customerNo/customerName`
- `customerMinWeight/customerMaxWeight`

这说明这层规格既包含内部工艺规格，也带客户口径下的重量上下限，不是单纯“尺寸字典”。

### 2. 新建款与改款规格不直接落生效表
规格链实际分三层：
1. `wh_new_sku_specs`
   - 挂在 `skuDetailId`
   - 对应新建款审批过程里的临时规格池
2. `wh_update_sku_specs`
   - 同时带 `skuNo + skuDetailId`
   - 对应改款审批过程里的临时规格池
3. `wh_sku_specs`
   - 审批生效后的正式规格表

所以 `sku-specs` 控制器暴露出来的是“已生效规格 CRUD”，但规格真实生命周期要和 `newsku / updatesku` 两条审批缓存链一起看。

### 3. 主服务本身很薄，主要承担生效表维护
`SkuSpecsServiceImpl` 当前做的事情很直接：
- `createSkuSpecs(...)`：插入 `wh_sku_specs`
- `updateSkuSpecs(...)`：直接 `updateById`
- `deleteSkuSpecs(...)`：直接 `deleteById`
- `getSkuSpecsBySkuNo(...)`：按 `skuNo` 查当前生效规格
- `deleteSkuSpecsBySkuNo(...)` / `deleteSkuSpecsBySkuNos(...)`：按款号批量删
- `batchSave(...)`：批量插入

也就是说，它更像是 `sku` 主链在审批落地后的规格持久层服务，而不是负责规格审批本身。

### 4. 新建款 / 改款缓存规格服务只负责按详情维度取数
`NewSkuSpecsServiceImpl` 与 `UpdateSkuSpecsServiceImpl` 都没有复杂业务逻辑，主要能力就是：
- 按 `skuDetailId` 或 `detailIds` 查询规格缓存
- 改款侧额外支持按 `skuNo / skuNos` 查询

这说明审批期间的规格数据主要是作为 `sku` 明细聚合视图的一部分被消费，真正的审批和生效驱动仍在 `sku` 主链，而不是规格模块自己。

### 5. `isDirect` 在规格层也出现，语义需要和 BOM 默认供应商区分
规格表和 BOM 头表都带 `isDirect`。但在规格层里，它显然不是“默认供应商”语义，而更像“直采/直营规格口径”标记。这个字段名跨层复用，会让阅读和维护很容易串义。

### 6. 删除链目前同样是裸删
能看到的删除能力包括：
- `deleteSkuSpecs(id)`
- `deleteSkuSpecsBySkuNo(skuNo)`
- `deleteSkuSpecsBySkuNos(skuNos)`

都没有看到：
- 被 `sku` 主表或审批缓存引用时的保护
- 对 `newsku / updatesku` 缓存规格的联动清理
- “规格仍被详情页、导出页、来源池使用时”的约束

## 关键实体
- `wh_sku_specs`：已生效规格表
- `wh_new_sku_specs`：新建款审批缓存规格表
- `wh_update_sku_specs`：改款审批缓存规格表
- `wh_sku`：规格所属主档

## 风险与待确认
1. `deleteSkuSpecs(...)`、`deleteSkuSpecsBySkuNo(...)`、`deleteSkuSpecsBySkuNos(...)` 当前都是物理删除，没看到对 `sku` 主档、审批缓存和历史查询的引用保护。
2. `updateSkuSpecs(...)` 与 `deleteSkuSpecs(...)` 里存在性校验被注释掉了，当前接口对不存在记录的保护较弱。
3. `isDirect` 在规格层和供应商 BOM 层复用同名字段，但语义明显不同，后续维护很容易误把它们当成同一口径。
4. `wh_new_sku_specs / wh_update_sku_specs / wh_sku_specs` 三层规格池之间的正式生效搬运链没有在规格服务层体现，真实落地动作需要继续依赖 `sku` 主链确认。

## 结论
SKU 规格应定位为 `sku` 主链下的“规格卫星层 + 审批缓存镜像层”。它自己没有独立审批流，但直接决定款式的尺寸、重量和客户口径，并通过新建款、改款缓存表参与 SKU 生效过程。
