# 供应商款式 / BOM
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`supplier-sku` 不是独立审批单，也不是普通明细表，而是 `sku` 主链下面的“供应商 BOM + 默认费用来源”子图。它把供应商款式头 `wh_supplier_sku`、辅石/辅料明细 `wh_supplier_sku_accessories`、半成品/配件明细 `wh_supplier_sku_part` 组合成一套可被 `sku` 建款、详情页、导出页、采购查询共同消费的 BOM 视图。

## 关键入口
- Controller: [SupplierSkuController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/suppliersku/SupplierSkuController.java)
- Service: [SupplierSkuServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/suppliersku/SupplierSkuServiceImpl.java)
- Satellite Service: [SupplierSkuAccessoriesServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/supplierskuaccessories/SupplierSkuAccessoriesServiceImpl.java)
- Satellite Service: [SupplierSkuPartServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/supplierskupart/SupplierSkuPartServiceImpl.java)

## 真实行为
### 1. 这层是 `sku` 默认供应商费用的来源池
`wh_supplier_sku` 头表不只存供应商归属，还直接承载一整组成本/销售费用字段，例如：
- `costGramCmtFee`
- `costItemCmtFee`
- `saleGramCmtFee`
- `moreItemCmtFee`
- `costEnamelFee/saleEnamelFee`
- `ropeWeavingFee/saleRopeFee`
- `testingFee/saleTestingFee`
- `costStoneFee/saleStoneFee`
- `costOtherFee/saleOtherFee`
- `skuCostOtherFee/skuSaleOtherFee`
- `basicGramCmtFee/moreGramCmtFee`

其中 `isDirect` 表示默认供应商行。`sku` 建款和改款时，真正投影回 `wh_sku` 主表费用字段的，就是这条默认供应商 BOM。

### 2. 主服务本身很薄，但它是 BOM 聚合入口
`SupplierSkuServiceImpl` 的单行增删改查几乎都是薄封装：
1. `createSupplierSku(...)` 直接插入 `wh_supplier_sku`
2. `updateSupplierSku(...)` 校验存在后直接更新
3. `deleteSupplierSku(...)` 校验存在后直接物理删除

真正重要的是它还承担了几类聚合查询：
- `getSupplierSkuBySkuNos(...)`：按 `skuNo` 批量取供应商行
- `getDirectListBySkuNos(...)`：按 `isDirect = "是"` 找默认供应商
- `getBomBySkuNos(...)`：左连 accessories + part，返回整套 BOM 视图
- `getBomList(...)`：给采购/BOM 查询页返回“头 + 辅石 + 配件”组合数据
- `getAccessoriesBySerialNos(...)`：按 `bomSerialNo` 反查饰石/辅料行

也就是说，这层虽然不像库存单据那样有审核流，但它是 SKU 详情页和导出链的真实 BOM 聚合宿主。

### 3. `bomSerialNo` 是跨表再聚合锚点，不只是展示字段
`SupplierSkuDO` 带有 `bomSerialNo`。在 `getAccessoriesBySerialNos(...)` 里，服务会：
1. 先从 `wh_supplier_sku` 取 `bomSerialNo`
2. 左连 `wh_supplier_sku_accessories`
3. 再把 `bomSerialNo` 重新映射进 accessories 的 `skuSupplierId`

这说明 `bomSerialNo` 已经被当作一条“BOM 组锚点”使用，而不是普通流水号。后续采购汇总、饰石查询和导出都可能依赖这条锚点链。

### 4. accessories 和 part 是纯卫星表，由头表驱动
`SupplierSkuAccessoriesServiceImpl` 与 `SupplierSkuPartServiceImpl` 的职责都非常薄：
- `deleteBySkuNos(...)`
- `listBySkuNos(...)`
- `clearBySkuNo(...)`

它们没有独立审批、没有独立业务校验，也没有自己的执行态；本质上是 `wh_supplier_sku` 头表之下的两张 BOM 卫星表。

其中：
- `wh_supplier_sku_accessories` 更偏“辅石/辅料费用明细”，字段包括石名、粒径、重量、颗数、成本/销售单价、分石费、件工费等。
- `wh_supplier_sku_part` 更偏“半成品/配件构成”，字段包括 `semisSkuNo`、规格、克重、数量、珐琅费、件工费、克工费等。

### 5. 采购 BOM 查询不是全量供应商视图
`getBomList(BomReqVO)` 当前写死了：
- `supplierCode = CommonConstant.SUPPLIER_00`

所以它不是“任意供应商 BOM 查询”，而更像“00 号商品部/默认采购口径”的专用视图。业务上如果有人把它理解成全供应商 BOM 检索，结果会偏。

### 6. 删除链目前是裸删
当前能看到的删除链包括：
- `deleteSupplierSku(id)` 物理删头表单行
- `deleteSupplierSkuBySkuNo(skuNo)` 按款号批量删头表
- `deleteSkuSpecsBySkuNos(...)` 名字像删规格，实际删的是 `wh_supplier_sku`
- accessories / part 的 `deleteBySkuNos(...)`、`clearBySkuNo(...)` 物理删卫星表

没看到：
- “被 `sku` 默认供应商引用时禁止删除”
- “存在 accessories / part 时先做完整级联”
- “历史导出/采购/成本引用保护”

## 关键实体
- `wh_supplier_sku`：供应商 BOM 头表，同时也是默认费用来源表
- `wh_supplier_sku_accessories`：辅石/辅料卫星明细
- `wh_supplier_sku_part`：半成品/配件卫星明细
- `wh_sku`：默认供应商费用投影目标

## 风险与待确认
1. `deleteSupplierSku(...)` / `deleteSupplierSkuBySkuNo(...)` / accessories / part 清理链目前都是物理删除，没看到对默认供应商引用、历史业务引用和完整级联的保护。
2. `deleteSkuSpecsBySkuNos(...)` 方法名明显与真实行为不符，它删的是 `SupplierSkuDO`，不是规格，后续维护很容易误用。
3. `getDirectListBySkuNos(...)` 用 `isDirect = "是"` 这类中文文案常量判断默认供应商，稳定性不如编码值。
4. `getBomList(...)` 当前硬编码 `supplierCode = 00`，需要确认它到底是“商品部专用视图”还是误把全量查询写窄了。

## 结论
供应商款式 / BOM 应定位为 `sku` 主链下的“默认供应商与费用来源子图”。它自己没有审核态，但会决定 SKU 主表默认费用、SKU 详情页 BOM 结构、采购 BOM 查询和导出内容，是商品域里非常关键的一层结构性主数据。
