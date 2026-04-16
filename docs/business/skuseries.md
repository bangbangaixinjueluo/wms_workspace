# 系列与套系映射
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
这一层不是单一资料表，而是 SKU 生态里的三段规则映射：
- `wh_sku_series`：系列主数据
- `wh_sku_series_mapping`：系列与 SKU 的关系表
- `wh_sku_set_mapping`：套系宿主表，会反写 SKU 主档上的 `setNo/setName`

其中“系列映射”偏关系维护，“套系映射”则已经带有真正的 SKU 主档回写和缓存失效动作。

## 关键入口
- Service: [SkuSeriesServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skuseries/SkuSeriesServiceImpl.java)
- Service: [SkuSeriesMappingServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skuseriesmapping/SkuSeriesMappingServiceImpl.java)
- Service: [SkuSetMappingServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skusetmapping/SkuSetMappingServiceImpl.java)

## 真实行为
### 1. 系列主数据是轻量规则主档
`SkuSeriesServiceImpl` 的主要职责是：
- `seriesNo` 唯一校验
- 创建/更新系列主数据
- 分页、导出、按编号回查
- `getMapByNos(...)` 给 SKU 审批域或详情页做系列编号到名称映射

它本身没有回写 SKU，也没有复杂状态机，更像一个轻量规则主档。

### 2. 系列映射是“一个系列挂多款式”的关系维护层
`SkuSeriesMappingServiceImpl.createSkuSeriesMapping(...)` 会：
1. 接收一个 `seriesId`
2. 接收多条 `skuIds`
3. 循环校验每个 SKU 是否存在
4. 校验 `skuId + seriesId` 不重复
5. 逐条插入 `wh_sku_series_mapping`

所以系列映射不是单行建关系，而是支持“把一组 SKU 批量挂到某个系列”。

### 3. 系列映射当前没有反写 SKU 主档
从实现上看，系列映射只维护 `wh_sku_series_mapping` 关系表，没有像套系那样把系列号/系列名反写回 `wh_sku`。这意味着：
- 系列查询更依赖 join 或关系表反查
- 它不像套系那样是 SKU 主档上的直读字段宿主

### 4. 套系映射是会直接改 SKU 主档的执行链
`SkuSetMappingServiceImpl.createSkuSetMapping(...)` 会：
1. 生成年度编号 `TS + 年份 + 5 位流水`
2. 按最多 6 个槽位把 `skuIds` 写进 `skuId1 ... skuId6`
3. 校验当前组合不与已有启用套系重复
4. 插入 `wh_sku_set_mapping`
5. 反写每个 SKU 的 `setNo/setName`
6. 删除对应 `biz_cache` 和 Redis `sku::id=*`

这说明套系映射不是普通关系表，而是一个会驱动 SKU 主档变化的规则宿主。

### 5. 套系更新会先清旧关联，再重挂新关联
`updateSkuSetMapping(...)` 的流程是：
1. 找到旧套系
2. 把旧套系上的每个 SKU 的 `setNo/setName` 清空
3. 清旧 SKU 缓存
4. 重建 `skuId1...skuId6`
5. 再把新套系号/名称写回新 SKU
6. 再次清缓存

因此套系修改不是单纯改一条关系记录，而是“先拆旧挂接，再建立新挂接”的两段式主档回写链。

### 6. 套系重复校验是按“整组 SKU 组合”判重
`checkSkuList(...)` 会把启用套系里的 `skuId1...skuId6` 排序后拼成字符串，再和当前这组 SKU 做比较。也就是说，业务上禁止的是“同一组款式重复组成多个启用套系”，而不是禁止单个 SKU 出现在多个套系里这一层表述。

## 风险与待确认
1. `deleteSkuSeries(...)`、`deleteSkuSeriesMapping(...)`、`deleteSkuSetMapping(...)` 当前都只看到物理删除，没看到对历史引用或主档反写一致性的保护。
2. 系列映射只维护关系表、不回写 `wh_sku`，而套系映射会直接回写 `wh_sku`，两者口径明显不一致，后续很容易让使用方误判。
3. 套系目前固定最多 6 个槽位 `skuId1...skuId6`，后续如果业务需要更多成员，会直接碰到表结构上限。
4. 套系重复校验按“整组 SKU 组合”判重，但没有明显限制单个 SKU 是否同时出现在多个不同套系组合里，需要确认业务是否允许。

## 结论
系列与套系映射应定位为“SKU 归属规则层”。系列主档和系列映射偏轻量关系维护；套系映射则已经进入“主档回写 + 缓存失效”的执行层，是更重的一条 SKU 归属链。
