# 原料档案

## 1. 模块定位

- 对应表：`wh_material`
- 入口：`MaterialController`、`MaterialServiceImpl`
- 角色：原料主数据宿主，同时承接钱包类型映射和原料本地缓存

## 2. 核心职责

- 维护原料编号、名称、状态、钱包类型
- 本地缓存 `materialNo -> MaterialDO`
- 为钱包、原料库存、原料日志、来料出料链提供原料映射

## 3. 代码结论

- `@PostConstruct initLocalCache()` 启动时构建本地缓存
- create/update/delete/status 变更后都会异步刷新缓存
- `getMaterialByMaterialNo(materialNo, true)` 会校验启用状态
- `fillData(...)` 会把 `walletTypeNo` 翻译成 `walletTypeName`

## 4. 关注点

- `⚠️ deleteMaterial(...)` 是物理删除，未见对钱包、原料库存、历史单据引用保护
- `⚠️` 本地缓存异步刷新，强一致性边界要注意
- `⚠️` 原料已经和钱包类型映射强绑定，改原料主数据会连动资金链
