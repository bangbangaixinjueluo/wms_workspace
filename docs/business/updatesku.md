# 款式修改单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`updatesku` 不是简单“修改申请”，而是 SKU 改款审批宿主链。它围绕正式 `sku` 主档，收敛了改款缓存、阶段审批、企微待办、批量工费修改、设计元素修改、上下架原因修改、模板导入导出和同步通知等动作。

## 关键入口
- Controller: [UpdateSkuController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/updatesku/UpdateSkuController.java)
- Business: [UpdateSkuBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/UpdateSkuBusiness.java)
- Cache Specs: [UpdateSkuSpecsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/updatesku/impl/UpdateSkuSpecsServiceImpl.java)

## 真实行为
### 1. 改款不是单点编辑，而是分阶段审批链
控制器动作已经把阶段切得很细：
- `saveDetailInfo`
- `updateBaseInfo`
- `submit` / `cancelSubmit`
- `approved` / `reject`
- `transferTo`
- `discardUpdateSku`
- 工费修改、批量工费修改
- 设计信息修改、设计元素修改、批量设计元素修改
- 下架原因 / 上架原因 / 批量原因修改
- 模板下载、导入、导出

并且控制器直接依赖 `UpdateSkuStageEnum`、`UpdateSkuStatusEnum`，说明改款链本身就不是单阶段，而是多阶段推进。

### 2. `UpdateSkuBusiness` 是正式 SKU 的差异宿主
它同时依赖：
- `UpdateSkuDetail/Specs/Supplier/SupplierAccessories/SupplierPart/Log`
- 正式 `Sku/SkuSpecs/SupplierSku/SupplierSkuAccessories/SupplierSkuPart`
- `BizCacheService`
- `RedisTemplate`
- 设计元素、分类、供应商、客户、材质、大类、生产工艺
- `NotifyMessageSendApi`
- `SseService`
- `PermissionApi`
- `QYWXUtils`

并定义了多组关键字段集合：
- `FEE_FIELDS`
- `SALE_FEE_FIELDS`
- `COST_FEE_FIELDS`
- `ACCESSORIES_FEE_FIELDS`

这说明改款链不是“整体覆盖 SKU”，而是一个围绕字段差异、费用差异和阶段差异展开的改款宿主。

### 3. 改款缓存比新建款更强调“差异字段”和“阶段模板”
从控制器和业务类可以看出：
- 导入模板会按 `state` 动态切换 `UpdateSkuStageEnum`
- 费用字段、设计元素、上下架原因都有单独批量入口
- 企微页会同时展示 `state + startState`

所以款式修改单不是简单复制一份 SKU 再编辑，而是“按阶段拆分编辑对象”的多入口审批链。

### 4. 改款期间的规格和 BOM 仍先落缓存表
改款链对应的缓存卫星层包括：
- `wh_update_sku_detail`
- `wh_update_sku_specs`
- `wh_update_sku_supplier`
- `wh_update_sku_supplier_accessories`
- `wh_update_sku_supplier_part`
- `wh_update_sku_log`

其中规格缓存 `wh_update_sku_specs` 同时带：
- `skuNo`
- `skuDetailId`

说明改款缓存既要挂正式款号，又要挂当前这次改款明细。

### 5. 企微、抄送、权限链比新建款更复杂
`pageQywx(...)` 里不仅有 `CopySetting`，还显式引入了：
- `PermissionApi`
- 不同阶段对应不同 permission key
- `state + startState`

这意味着改款待办的可见性，除了当前阶段外，还受阶段起点、抄送配置和菜单权限共同影响。

## 风险与待确认
1. `UpdateSkuBusiness` 把改款缓存、字段差异、企微待办、导入模板、权限判定都放在同一宿主里，复杂度明显高于普通审批模块。
2. `FEE_FIELDS`、`SALE_FEE_FIELDS`、`COST_FEE_FIELDS` 等字段集合是硬编码常量，后续字段增减如果漏改，改款审批口径会直接失真。
3. 改款缓存同时依赖正式 `sku` 主档和缓存卫星表，需要继续确认审批通过时哪些字段走覆盖、哪些字段走差异合并。
4. 按阶段动态切模板和批量入口，意味着前后端都强依赖 `UpdateSkuStageEnum/StatusEnum`；状态枚举一旦调整，导入导出和企微页都要回归。

## 结论
款式修改单应定位为“SKU 改款审批宿主链”。它不是普通改单，而是围绕正式 SKU 的多阶段差异审批系统，核心价值在于把费用、设计、上下架、规格、BOM 和协同待办统一收敛到一条改款执行链里。
