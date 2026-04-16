# 品类档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
品类档案不是业务执行单，而是商品结构层里的一级品类主数据。它不仅维护品类编号和名称，还承接子品类、五级分类、四级分类、规格、产品资料这些下游结构，并把品类信息同步到 SKU 和商城商品分类树。

## 关键入口
- Controller: [CategoryController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/category/CategoryController.java)
- Business: [CategoryBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/CategoryBusiness.java)
- Service: [CategoryServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/category/CategoryServiceImpl.java)

## 真实行为
### 1. 建档的显式强校验主要是“品类编号唯一”
`createCategory(...)` 当前主要校验：
- `categoryNo` 唯一

原本对 `code` 的唯一校验被注释掉了。建档成功后会：
1. 写入 `wh_category`
2. 通过 `CategoryBusiness` 调 `productCategoryApi.createIfAbsent(...)`
3. 在商城商品分类树中补一条顶级分类节点

### 2. 修改品类会同步更新 SKU 和商城分类树
`updateCategory(...)` 会：
1. 校验编号唯一
2. 先执行 `skuMapper.updateCategory(id, categoryNo, categoryName)`
3. 更新 `wh_category`
4. 再次调用 `productCategoryApi.createIfAbsent(...)`

也就是说，品类改名/改编号至少会影响两边：
- WMS 内部 SKU 冗余字段
- Mall 侧商品分类树节点

### 3. 品类详情页本质是一个结构聚合视图
`getSimpleListDetail(id)` 不是简单查单表，而是一次把这个品类下的：
- `categoryFive`
- `categoryFour`
- `categorySpec`
- `categorySub`
- `categoryProduct`

一起聚合出来。说明一级品类是商品结构域的宿主节点，不只是一个下拉字典。

### 4. 删除是裸删，没看到对子结构和商品资料的保护
`deleteCategory(id)` 只校验存在后物理删除 `wh_category`，没看到对：
- 子品类
- 四级/五级分类
- 规格
- 产品资料
- SKU
- 商城分类树

做级联处理或删除拦截。

## 关键实体
- `wh_category`：一级品类主档。
- `wh_category_sub`：子品类。
- `wh_category_four` / `wh_category_five` / `wh_category_spec`：品类下级结构。
- `wh_product`：产品资料。
- `wh_sku`：品类编号/名称冗余回写目标。
- `mall product_category`：商城侧同步分类树。

## 风险与待确认
1. `deleteCategory(...)` 没看到对子品类、规格、产品资料、SKU、商城分类树的级联清理或引用保护。
2. `createCategory(...)` / `updateCategory(...)` 里 `code` 唯一校验被整段注释，需确认该字段是否已废弃。
3. `productCategoryApi.createIfAbsent(...)` 只看到“若不存在则创建”，没看到名称变更时是否会同步更新商城分类名称。
4. `getByNamesMap(...)` 里的 `deleted` 过滤仍复用了启用状态值，需要确认。

## 结论
品类档案应定位为“商品结构一级宿主”。它上接商城分类树，下承子品类、规格和产品资料，还会继续影响 SKU 冗余字段与多个商品结构查询页面。
