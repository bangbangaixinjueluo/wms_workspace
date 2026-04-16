# 大类档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
大类档案不是业务执行单，而是 SKU、库存、商品归类、计价方式和产线属性共用的商品主数据节点。它不仅维护大类基础信息，还决定了 SKU 上冗余的大类字段，以及一些销售/存货计价和物资分类语义。

## 关键入口
- Controller: [BigClassController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/bigclass/BigClassController.java)
- Service: [BigClassServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/bigclass/BigClassServiceImpl.java)

## 真实行为
### 1. 建档最核心的是“大类编号唯一”
`createBigClass(...)` 当前显式校验的是：
- `bigClassNo` 在未删除数据里唯一。

通过后直接写入 `wh_big_class`。当前没看到像材质那样对名称唯一、编码格式之类的额外强校验。

### 2. 修改大类会同步更新 SKU 冗余大类信息
`updateBigClass(...)` 在更新主表前会先执行：
- `skuMapper.updateBigClass(id, bigClassNo, bigClassName, bigType)`

说明 SKU 里也冗余保存了：
- 大类编号
- 大类名称
- 大分类 `bigType`

因此大类改名/改编号不是单表动作，而是主档和 SKU 冗余字段同步更新。

### 3. 删除也是裸删，未见引用保护
`deleteBigClass(id)` 只做：
1. 校验存在。
2. `bigClassMapper.deleteById(id)`。

没看到对 SKU、库存、商品档案、材质归类或历史单据引用的删除阻断。

### 4. 大类不只是名字，还带计价和物资分类语义
`BigClassDO` 里除了编号和名称，还有：
- `salePriceMode`
- `storePriceMode`
- `mainUint`
- `subUint`
- `goodsType`
- `goodsClassify`
- `bigType`
- `productionLineCode/productionLineName`

这说明大类不只是展示归类，还挂着销售计价、存货计价、单位和物资分类这些规则字段。

### 5. 启用态是下拉和名称映射的重要过滤条件
`list-all-simple` 只取 `status = 0` 的启用大类；`getByNamesMap(...)` 也只会返回启用且未删除的大类。说明大类状态会直接影响很多前端下拉和导入名称映射。

## 关键实体
- `wh_big_class`：大类主档。
- `wh_sku`：大类编号/名称/大分类冗余回写目标。
- 商品归类/计价语义：通过 `bigType/salePriceMode/storePriceMode/goodsType/goodsClassify` 承接。

## 风险与待确认
1. `deleteBigClass(...)` 是物理删除，未见对 SKU、库存、商品档案和历史业务引用的保护。
2. `createBigClass(...)` 只显式校验了编号唯一，没看到名称唯一保护，需要确认业务上是否允许重名大类。
3. `updateBigClass(...)` 当前只同步回写了 SKU 冗余字段，未见其他商品主数据或缓存同步。
4. `getByNamesMap(...)` 同样把 `deleted` 过滤写成了 `eq(BigClassDO::getDeleted, CommonStatusEnum.ENABLE.getStatus())`，需要确认这不是偶然复用状态值。

## 结论
大类档案应定位为“商品归类与计价规则主数据”。它不是纯字典，因为一旦大类变化，会继续影响 SKU 冗余大类字段，以及多个商品、库存和销售口径的分类语义。
