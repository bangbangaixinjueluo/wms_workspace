# 款式库
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`sku` 不是简单商品主档，而是“可执行商品对象”的核心宿主链。它把大类、材质、品类、五级分类、默认供应商、BOM、图片、审批状态、搜索标签、默认工费和导出权限口径聚到一起，是库存、单件、销售、委外、来料出料等业务链共同依赖的商品对象。

## 关键入口
- Controller: [SkuController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/sku/SkuController.java)
- Business: [SkuBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/SkuBusiness.java)
- Service: [SkuServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/sku/SkuServiceImpl.java)

## 真实行为
### 1. 建款不是插一张 `wh_sku`
`createSku(...)` 先做一串结构校验：
1. `productionLineCode` 必须是 2 位 16 进制编码。
2. 产线编码必须能在字典 `product_line` 中解析。
3. 若五级分类含“固口”，规格必填。
4. `categoryFiveNo` 必须存在，且其归属品类必须等于当前 `categoryId`。
5. `bigClassId` 必须存在。
6. 供应商 BOM 列表不能为空。
7. 必须选出默认供应商。

随后会把大类信息冗余进 SKU：
- `bigClassNo/bigClassName`
- `bigTypeNo/bigTypeName`

再从默认供应商行把一整组成本/销售费用复制到 SKU 主表，例如：
- `costGramCmtFee`
- `costItemCmtFee`
- `saleGramCmtFee`
- `moreItemCmtFee`
- `costEnamelFee/saleEnamelFee`
- `costStoneFee/saleStoneFee`
- `basicGramCmtFee/moreGramCmtFee`

所以 SKU 主表上的很多费用字段，本质上是默认供应商 BOM 的投影结果。

### 2. 默认状态是“禁用 + 走审批”
`SkuService.createSku(SkuDO)` 会把新建 SKU 默认写成：
- `skuStatus = DISABLE`

`SkuDO` 同时还带：
- `processResult`
- `processUserId`
- `processInstanceId`
- `approvedTime`

而 `SkuBusiness` 里有：
- `submitProcess(...)`
- `updateProcessResult(...)`
- `biz_cache` 读写

说明 SKU 和客户档案类似，也有“主表当前态 + 审批缓存态”的结构，只是业务内容更重。

### 3. 审批通过后，真正生效的是缓存里的版本
`updateProcessResult(...)` 审批通过时会：
1. 从 `biz_cache` 读取待生效版 `SkuDO`
2. 回写 `processResult`
3. 回写 `approvedMan/approvedTime`
4. 用缓存版覆盖更新 SKU 主表
5. 删除缓存

审批不通过则只更新：
- `processResult`
- `approvedMan`

也就是说，SKU 详情页看到的内容有可能是“主表 + 待审批缓存混合视图”。

### 4. SKU 详情不是单表详情，而是 BOM 聚合详情
`getSku(id)` 除了主表字段，还会继续补：
- 图片数组
- 规格列表 `SkuSpecs`
- 供应商 BOM 列表 `SupplierSku`
- 配件列表 `SupplierSkuAccessories`
- 配件/半成品件列表 `SupplierSkuPart`

因此一条 SKU 实际上是“主档 + 多套供应商工费/BOM + 规格 + 图片”的聚合对象。

### 5. 搜索和导出都不是纯查询，带权限和外部能力
`getSkuVOPage(...)` / 企微搜索链说明 SKU 还是统一搜索入口：
- 可按销售件数、辅料镶石、供应商、设计元素筛选
- 支持语音/关键词/图片三类搜索分支
- 会查销售统计决定是否展示销量/毛利字段
- 会补默认供应商成本口径、设计元素名称、生产工艺名称

导出还会根据角色控制销售件数等字段可见性。

### 6. 删除仍然是裸删
`deleteSku(id)` 当前只校验存在后直接删除 `wh_sku`，没看到对：
- `sku_stock`
- 单件
- BOM
- 销售/库存/来料出料历史引用

做保护。

## 关键实体
- `wh_sku`：款式主档。
- `biz_cache`：待审批版本缓存。
- `wh_supplier_sku`：供应商 BOM 主表。
- `wh_supplier_sku_accessories`：辅石/配件明细。
- `wh_supplier_sku_part`：配件/半成品件明细。
- `wh_sku_specs`：规格集合。
- `wh_sku_stock`：SKU 库存。

## 风险与待确认
1. `deleteSku(...)` 没看到对库存、单件、BOM、销售和历史业务单据的引用保护。
2. `updateProcessResult(...)` 审批通过后只看到主表覆盖更新，注释里提到的“同步商城/同步标签”等分支目前被注释，需确认真实生效范围。
3. 默认供应商费用会整体投影到 SKU 主表，后续如果只改供应商 BOM 而不回刷 SKU，费用口径可能漂移。
4. 搜索链里混入了外部 AI/企微/图片搜索逻辑，维护时很容易把“商品主档”和“搜索服务”耦在一起。

## 结论
SKU 应定位为“商品执行对象宿主链”，不是普通商品资料表。它上承商品结构与审批，下接默认供应商 BOM、库存、单件、搜索和导出权限，是业务域里真正被广泛消费的商品核心实体。
