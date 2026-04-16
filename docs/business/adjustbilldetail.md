# 单件调整明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`adjustbilldetail` 是单件调整单的事实明细层，而且比普通 detail 更重。它不仅存调整前后明细，还会额外挂接配件调整列表和 SKU 展示补数。

## 关键入口
- Service: [AdjustBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/adjustbilldetail/AdjustBillDetailServiceImpl.java)

## 真实行为
### 1. 明细页会补 SKU 和配件
`getAdjustBillDetailListByBillId(id)` 会：
- 批量查 `sku`
- 回补 `categoryId/imageUrl`
- 再按 `detailId` 聚合 `adjustbilldetailaccessories`

### 2. 这层承担批处理写入
- `saveBatch(...)`
- `deleteBatchByIds(...)`
- `updateBatch(...)`
都已经在 detail 层提供，说明主单执行链高度依赖这张表做整批重建。

## 在主链中的作用
- `adjustbill`
  - 主单负责改单件、改单件库存和成品库存；明细层负责“改了哪些字段、哪些配件”的事实落点。
- `adjustbilldetailaccessories`
  - 配件卫星表是挂在 detail 层上的，不是直接挂主单。

## 风险与待确认
1. `⚠️` `deleteAdjustBillDetail(...)` 当前物理删除，未见主单状态限制。
2. `⚠️` `getAdjustBillDetailListByBillId(...)` 强依赖 `skuMap.containsKey(skuId)`，历史脏数据会直接断言失败。
3. `⚠️` `updateBatch(...)` 批量更新默认按 1000 分批，后续明细量放大时要关注批次边界和事务时长。

