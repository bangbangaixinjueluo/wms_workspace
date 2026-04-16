# 款式模号

## 模块定位
- 对应表：`wh_sku_model`
- 入口：`SkuModelController`、`SkuModelServiceImpl`
- 这不是普通字典表，而是“SKU + 重量段 -> 模号”的派生主数据宿主，同时还承接老庙共享状态开关。

## 主数据职责
- 主字段组合是：
  - `modelNo`
  - `skuId / skuNo`
  - `segmentNo`
  - `bigType`
  - `minGramWeight / maxGramWeight`
  - `sampleGramWeight`
  - `shareStatusLm`
- `modelNo` 当前按全表唯一校验。
- `segmentNo` 为空时默认写 `00`。

## 建档/改档逻辑
- 创建和修改都会先校验正式 `skuNo` 存在。
- 再按 `segmentNo + sku.bigTypeNo` 反查重量段，并把重量段上下限与 SKU 样版克重投影到模号表。
- 控制器单独暴露了 `update-share-status-lm`，可直接改 `shareStatusLm`。

## 自动生成链
- `genSkuModel(skuDO, goldWeight)`：
  - 先按金重命中重量段
  - 再生成 `modelNo = skuNo[-segmentNo]`
  - 不存在则新增，存在且重量段变化则更新
- `genSkuModelByModelNo(...)`：
  - 支持按传入模号反推 `segmentNo`
  - 也支持按最小/最大金重均值重算重量段
- `batchSaveOrUpdate(...)`：
  - 新增批次按 `modelNo` 去重
  - 已有 `id` 的按批次更新

## 查询能力
- 支持按 `skuNo` 直接查模号列表。
- 分页/导出可按：
  - `modelNo`
  - `skuNo`
  - `segmentNo`
  - `remark`
  - `shareStatusLm`
- 回显时还会再次按重量段补 `minWeight/maxWeight`。

## 关键结论
- 这层不是纯手工维护主数据，而是 SKU 重量段分桶后的“模号缓存层”。
- `shareStatusLm` 说明模号层已经直接承接老庙共享开关，不只是内部编码资料。
- 真正大量写入这张表的并不只有后台 CRUD，还有 `genSkuModel*` 这类业务自动生成链。

## 风险与疑点
- `⚠️` `createSkuModel()` / `updateSkuModel()` 里重量段存在性判断明显反了：当前代码是 `weightSegmentDO != null` 就抛“重量段不存在”，但后面又直接读取 `weightSegmentDO.getMinWeight()`。
- `⚠️` 删除是物理删除，没看到对老庙共享、共享库存或历史模号引用的保护。
- `⚠️` `fillData(...)` 固定按 `BigTypeEnum.PLAIN_GOLD` 回查重量段，不一定等于模号自身存的 `bigType`。
- `⚠️` `update-share-status-lm` 的权限挂的是 `system:user:update`，和模块自身权限体系不一致。
