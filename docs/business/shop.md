# 网点档案

## 1. 模块定位

- 对应表：`wh_shop`
- 入口：`ShopController`、`ShopServiceImpl`
- 角色：网点主数据宿主，承接部门绑定、用户可见网点、默认网点和名称映射

## 2. 核心职责

- 维护网点编号、名称、部门归属和状态
- 支持按部门反查网点、按用户返回可见网点
- 提供默认网点、名称映射、导出分页
- 自带缓存：`cacheNames = "shop"`

## 3. 代码结论

- `create/update` 都会校验 `deptId` 对应部门存在
- `getSimpleListByUser()`：
  - 用户若直接绑定 `shopIds`，优先取绑定网点；
  - 否则按用户所属部门往下取三级部门下的网点
- `getDefault()` 会取启用状态下 ID 最小的网点
- `deleteShop(...)` 当前是物理删除

## 4. 关注点

- `⚠️ deleteShop(...)` 未见对仓库、银行卡、库存、单据历史引用保护
- `⚠️ getDefault()` 直接取首条启用网点，默认网点口径需要确认是否稳定
