# 新建款管理
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`newsku` 不是简单的“建款申请表”，而是 SKU 新建审批宿主链。它把基础资料、规格、供应商 BOM、辅石/配件、日志、批次、企微待办、导入导出、审批转办、消息提醒都收敛在 `NewSkuBusiness` 这一条主链里。

## 关键入口
- Controller: [NewSkuController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/newsku/NewSkuController.java)
- Business: [NewSkuBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/NewSkuBusiness.java)
- Cache Specs: [NewSkuSpecsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/newsku/impl/NewSkuSpecsServiceImpl.java)

## 真实行为
### 1. 这是一条完整的新建款审批流程，不是单表缓存
控制器暴露的动作覆盖了：
- `saveBaseInfo` / `updateBaseInfo`
- `submit` / `cancelSubmit`
- `approved` / `reject`
- `transferTo`
- `discardNewSku`
- `synchronize` / `back`
- 工费、款名、设计信息批量修改
- 明细导入导出
- 企微待办、企微详情、消息数

这说明新建款不是“录完资料直接进 BPM”，而是有自己的阶段推进、回退、同步和批量修订链。

### 2. `NewSkuBusiness` 是聚合宿主，不只是审批服务
`NewSkuBusiness` 同时依赖：
- `NewSkuDetail/Specs/Supplier/SupplierAccessories/SupplierPart/Log`
- 正式 `Sku/SkuSpecs/SupplierSku/SupplierSkuAccessories/SupplierSkuPart`
- `Category/CategorySub/CategoryFive/BigClass/Texture/Supplier/Customer`
- `BpmProcessInstanceApi`
- `NotifyMessageSendApi`
- `SseService`
- `FileApi`
- `QYWXUtils`
- `SemisSKuApi`

这意味着它不是只维护“新建款缓存”，而是负责把商品结构校验、审批流、通知流、企微流和半成品联动串成一条完整业务链。

### 3. 新建款期间，规格和 BOM 都先落缓存池
从这条链上的服务结构可以看出，新建款缓存不只是一张头表，而是至少包含：
- `wh_new_sku_detail`
- `wh_new_sku_specs`
- `wh_new_sku_supplier`
- `wh_new_sku_supplier_accessories`
- `wh_new_sku_supplier_part`
- `wh_new_sku_log`

也就是说，新建款审批前看到的规格、供应商、辅石、配件都不是正式 `sku` 侧表，而是缓存态聚合结果。

### 4. 企微待办和抄送是新建款链内置能力
`pageQywx(...)`、`qywxBillDetails(...)`、`qywxSkuDetails(...)`、`messageCount()` 以及 `getCcRecipient(...)` / `getTransfer(...)` 说明：
- 新建款有专门的企业微信待办视图
- 待办按 `latestOperateBatch` 聚合
- 抄送人可见状态受 `CopySetting + menu permission` 影响
- 审批/驳回历史不是直接看明细状态，而是通过 `NewSkuLog` 反推

因此它是一条“审批执行 + 协同通知”复合链。

### 5. 导入导出不是简单模板上传
新建款控制器提供：
- Excel 模板下载
- 明细导入
- 明细导出

而 `NewSkuBusiness` 又依赖 `NewSkuDetailExcelAnalysisBO`、文件服务、字典、商品结构服务和大量校验工具。这说明导入链会把 Excel 解析、字段标准化、商品结构校验、缓存明细落表一起做完，不是薄导入。

## 风险与待确认
1. `NewSkuBusiness` 聚合依赖过重，把审批、导入、通知、企微、半成品联动都耦在一起，后续改动成本很高。
2. 新建款规格/BOM/辅石/配件都先落缓存表，必须确认审批通过时正式搬运到 `sku` 主链的动作是否完整覆盖所有卫星表。
3. 企微待办和抄送可见状态依赖 `CopySetting + permission` 动态组合，状态口径一旦改动，很容易出现“页面可见但待办不可见”。
4. `synchronize(...)`、`back(...)` 这类动作说明存在阶段内回退/重算逻辑，后续需要确认它们是否会影响缓存卫星表一致性。

## 结论
新建款管理应定位为“SKU 新建审批宿主链”。它上承商品结构和导入校验，下接缓存规格/BOM/辅石/配件、企微待办、通知与审批流，是 SKU 正式生效前最完整的一层前置业务链。
