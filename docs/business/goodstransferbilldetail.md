# 成品调拨明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`goodstransferbilldetail` 是成品调拨单的事实明细层。它本身服务层不重，但它承担了按单号、按单头读取整组明细的职责，是成品调拨主链的基础卫星表。

## 关键入口
- Service: [GoodsTransferBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodstransferbilldetail/GoodsTransferBillDetailServiceImpl.java)

## 真实行为
- `getGoodsTransferBillDetailsByBillNo(billNo)`
  - 按业务单号读取整组明细。
- `selectListByBillId(billId)`
  - 按主键 `billId` 读取整组明细。
- `getGoodsTransferBillDetailPage(...) / getGoodsTransferBillDetailList(...)`
  - 承接分页与导出查询。
- `deleteGoodsTransferBillDetail(id)`
  - 当前是先校验存在，再物理删除单条。

## 在主链中的作用
- `goodstransferbill`
  - 主单负责调拨状态机、来源单回写和库存动作；明细层负责实际调拨事实落表。

## 当前结论
- 这层虽然比 `transferbilldetail` 更薄，但仍然是成品调拨链不可缺的事实层。
- 调拨主单想重建、查详情、导出，都会依赖这张表。

## 风险与待确认
1. `⚠️` `deleteGoodsTransferBillDetail(...)` 当前物理删除，未见主单状态限制。
2. `⚠️` 服务层没有补数逻辑，说明展示口径强依赖 mapper 或上层聚合，后续改展示要先确认真实来源。

