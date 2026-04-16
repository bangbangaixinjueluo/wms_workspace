# 辅料档案

## 1. 模块定位

- 对应表：`wh_accessories_archives`
- 入口：`AccessoriesArchivesController`、`AccessoriesArchivesServiceImpl`
- 角色：辅料域最外层档案池，提供批量导入、分页、导出与基础资料查询

## 2. 核心职责

- 维护辅料档案基础数据
- 为辅料分类、辅料商品、辅料单据提供来源资料
- 支持 `batchSave(...)` 批量落档

## 3. 代码结论

- 服务层目前是薄服务，`create/update/get/page/list` 都直接落在 `AccessoriesArchivesMapper`
- `batchSave(...)` 只判断入参是否为空后直接 `insertBatch(...)`
- 当前没看到与辅料库存、领用、入库、出库的强约束联动

## 4. 关注点

- `⚠️ delete` 链路当前未在 service 暴露，后续如果补删档，需要确认历史单据引用保护
- `⚠️ batchSave(...)` 没看到显式去重/存在性校验，导入时容易重复落档
- `⚠️` 当前更像“来源池”，不是最终业务执行宿主，改档后对下游是否回刷需要继续确认
