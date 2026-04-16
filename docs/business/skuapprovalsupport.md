# SKU 审批域辅助链
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
这层不是独立表模块，而是 `newsku / updatesku` 审批域里的辅助执行链，主要包括：
- Excel 导入分析 BO
- 批量工费/款名/设计元素/上下架原因修改
- `synchronize(...)` 正式生效链
- `back(...)` 阶段回退链

它决定审批域不只是“保存缓存”，而是真正能做批量导入、阶段回退和正式同步的执行系统。

## 关键入口
- Business: [NewSkuBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/NewSkuBusiness.java)
- Business: [UpdateSkuBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/UpdateSkuBusiness.java)
- BO: [NewSkuDetailExcelAnalysisBO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/newsku/bo/NewSkuDetailExcelAnalysisBO.java)
- BO: [UpdateSkuDetailExcelAnalysisBO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/updatesku/bo/UpdateSkuDetailExcelAnalysisBO.java)

## 真实行为
### 1. 导入不是“Excel 转表”，而是整套审批缓存装配
新建款 `importDetail(file)` 会：
1. 先构造 `NewSkuDetailExcelAnalysisBO`
2. 读取 Excel 并做 `analysis(...)`
3. 校验失败则直接输出 `errorMessage`
4. 上传图片并重命名
5. 生成批次号 `XK`
6. 落 `wh_new_sku_detail`
7. 再按 index 绑定并落：
   - `wh_new_sku_specs`
   - `wh_new_sku_supplier`
   - `wh_new_sku_supplier_accessories`
   - `wh_new_sku_supplier_part`
8. 最后补 `wh_new_sku_log`

改款 `importDetail(file, state)` 更复杂，会先根据 `state -> UpdateSkuStageEnum` 决定导入模板和分析分支，然后再分别走：
- 基础资料导入
- 工费导入
- 设计信息导入
- 设计元素导入
- 下架原因导入
- 上架原因导入

说明导入链本身就是审批域的第一层业务分发器。

### 2. 导入分析 BO 是“跨主数据预加载容器”
`NewSkuDetailExcelAnalysisBO` 和 `UpdateSkuDetailExcelAnalysisBO` 都不是简单 DTO，它们会同时缓存：
- 待落明细、规格、supplier、辅石、配件
- 大类、材质、品类、子品类、五级分类、供应商、客户、系列、套系、设计元素、生产工艺、半成品等主数据映射
- `errorMessage`
- 改款侧额外还有 `warnMessage`

也就是说，Excel 导入并不是边读边落表，而是先把“审批缓存数据 + 主数据映射 + 校验结果”整批装进 BO，再统一分析。

### 3. 新建款同步是“缓存域 -> 正式商品域”的整套搬运
`NewSkuBusiness.synchronize(...)` 只允许 `FINISHED` 状态，然后会整条搬运：
- `wh_new_sku_detail -> wh_sku`
- `wh_new_sku_specs -> wh_sku_specs`
- `wh_new_sku_supplier -> wh_supplier_sku`
- `wh_new_sku_supplier_part -> wh_supplier_sku_part`
- `wh_new_sku_supplier_accessories -> wh_supplier_sku_accessories`

同步时还会：
- 自动生成正式 `skuNo`
- 处理主图、辅图、明细图路径重命名
- 生成新的 `bomSerialNo`
- 批量写正式表
- 把新建款明细状态改成 `SYNCHRONIZED`
- 写同步日志
- 调 `fileApi.updateFilePath(...)`
- 异步发站内信

所以“同步”不是状态变更，而是真正的正式生效动作。

### 4. 改款同步不是一条链，而是按改款类型拆四条同步分支
从 `UpdateSkuBusiness` 的实现看，改款同步至少拆成：
- `syncNormal(...)`
  - 主档、规格、BOM、图片整套覆盖
  - 清 `biz_cache`
  - 清 Redis `sku::id=*`
- `syncDesign(...)`
  - 只同步设计元素、工艺、别名标签等设计类字段
- `syncDown(...)`
  - 只同步下架状态和下架原因
- `syncShelves(...)`
  - 只同步上架状态并清空下架原因

也就是说，改款同步不是“统一覆盖正式 SKU”，而是按改款阶段和类型拆分成多条正式生效链。

### 5. 回退不是驳回，而是阶段倒退
新建款和改款的 `back(...)` 都要求：
- 传入原因
- 同批次状态一致
- 只能从指定状态回退到上一阶段

例如：
- 工费阶段可回退到基础阶段
- 设计阶段可回退到工费阶段

回退后会：
- 把明细状态改成上一阶段的 `REJECTED_*`
- 生成新的批次号
- 写回退日志
- 发消息给上一阶段处理人

这说明“回退”是审批域内部的阶段逆流，不等于普通“驳回申请人”。

### 6. 批量修改链已经有独立业务口径
改款侧的批量修改尤其重，包括：
- `updateCmtFeeBatch(...)`
- `updateCostCmtFeeBatch(...)`
- `updateSkuNameBatch(...)`
- `updateDesignElementBatch(...)`
- `updateReasonBatch(...)`
- `updateRemoveReasonBatch(...)`
- `updateShelvesReasonBatch(...)`

其中设计元素批量修改还会：
- 校验设计元素是否存在
- 校验分类归属
- 校验设计元素 2/3/4 的字典映射
- 规格化生产工艺名称
- 批量写日志

上下架原因批量修改则会根据 `state` 反推出 `skuStatus` 并生成不同备注日志。说明批量修改不是薄更新，而是带完整业务解释链。

## 风险与待确认
1. 新建款同步会直接生成正式 `skuNo`、复制图片、落正式 BOM 和规格，属于高风险正式生效动作，不能被误当成普通状态切换。
2. 改款同步分成 `normal/design/down/shelves` 多条分支，后续如果新增改款类型，很容易漏同步对应正式字段。
3. 导入分析 BO 同时承载主数据映射、错误收集和待落缓存对象，复杂度很高，一旦某个映射源改名，整条导入链都会受影响。
4. 改款同步后会清 `biz_cache` 和 Redis `sku::id=*`，说明正式商品读取缓存和审批缓存是并存的，缓存失效策略必须和生效动作严格同步。

## 结论
SKU 审批域辅助链应定位为“审批生态的执行引擎”。真正让 `newsku / updatesku` 成为业务系统的，不只是明细和缓存表，而是这套导入分析、批量修改、同步生效和阶段回退机制。
