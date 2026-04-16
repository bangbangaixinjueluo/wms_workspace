# 子品类
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
子品类是一级品类下的第二层商品结构主数据。它既要挂在父品类下管理，又要把子品类编号同步回 SKU，并在商城分类树里作为一级品类的子节点存在。

## 关键入口
- Controller: [CategorySubController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/categorysub/CategorySubController.java)
- Business: [CategoryBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/CategoryBusiness.java)
- Service: [CategorySubServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/categorysub/CategorySubServiceImpl.java)

## 真实行为
### 1. 建档时会先校验子品类编号唯一
`createCategorySub(...)` 会校验：
- `subCategoryNo` 唯一

建档后会立即执行两件事：
1. 写入 `wh_category_sub`
2. `skuMapper.updateSubCategoryByName(categoryId, subCategoryNo, subCategoryName)`

最后在 `CategoryBusiness` 中：
3. 调 `productCategoryApi.createIfAbsent(...)`
4. 把它同步成商城分类树里挂在父品类下的子节点

### 2. 修改子品类也会同步更新 SKU
`updateCategorySub(...)` 会：
1. 校验子品类存在
2. 校验新的 `subCategoryNo` 不和别人冲突
3. 更新 `wh_category_sub`
4. 再调 `skuMapper.updateSubCategoryByName(...)`
5. 通过 `CategoryBusiness` 同步商城分类节点

而且这里的 SKU 同步是“按父品类 + 子品类名称”去更新，代码注释也说明它是在兼容“导入时按名称导入”的口径。

### 3. 启用列表和简单列表口径不完全一致
- `getEnableList()` 当前只按 `deleted = 0` 过滤，没显式卡 `status`
- `getSimpleList()` 甚至只是查部分字段，没有显式启用过滤

所以“启用子品类”接口和“简单列表”接口的口径并不完全一致。

### 4. 删除同样是裸删
`deleteCategorySub(id)` 只校验存在后物理删除，没看到对 SKU、产品资料、商城分类树节点的回收或保护。

## 关键实体
- `wh_category_sub`：子品类主档。
- `wh_category`：父品类。
- `wh_sku`：子品类编号冗余回写目标。
- `mall product_category`：商城侧同步子节点。

## 风险与待确认
1. `deleteCategorySub(...)` 没看到对 SKU、产品资料和商城分类树的级联清理或引用保护。
2. `getEnableList()` 只按 `deleted = 0` 过滤，没按 `status = ENABLE` 过滤，名字和实际口径不完全一致。
3. `updateSubCategoryByName(...)` 是按名称更新 SKU，若历史 SKU 上名称与当前主档不一致，可能漏刷。
4. 商城侧仍然只看到 `createIfAbsent(...)`，没看到改名时是否会同步更新商城分类名称。

## 结论
子品类应定位为“一级品类下的第二层结构节点”，不是孤立字典。它直接联动 SKU 和商城分类树，并决定很多商品结构查询与导入匹配口径。
