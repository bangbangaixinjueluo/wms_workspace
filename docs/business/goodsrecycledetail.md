# 成品转料明细

## 模块定位
- 对应表：`wh_goods_recycle_detail`
- 入口：`GoodsRecycleDetailController`、`GoodsRecycleDetailServiceImpl`
- 这不是独立执行单，而是成品转料单的明细宿主，同时也是成品转料报表分页和导出的真实查询层。

## 主数据职责
- 关键字段包括：
  - `billId/billNo`
  - 成品 SKU/图片/分类字段
  - 数量、金重、重量等明细口径
- 服务层一部分是 CRUD，一部分是 `pageData/listData/listDataSummary` 这组报表型查询。

## 主要业务动作
- `create/update/delete`
  - 基础明细 CRUD。
- `getGoodsRecycleDetailByBillNo(...)`
  - 主单审核/反审时按单号批量取明细。
- `deleteGoodsRecycleDetailByBillNo(...)`
  - 主单重建时按单号整批删除明细。
- `insertBatch(...)`
  - 成品转料主链批量写明细。

## 报表与展示口径
- `pageData(...)`
  - 走 mapper 自定义分页，不是代码生成分页。
- `listData(...)`
  - 先查明细，再额外追加一条 `listDataSummary(...)` 汇总行。
- `getGoodsRecycleDetailByBillId(...)`
  - 会把主单的 `shopId/wareId` 投影到明细视图。
  - 再按 `skuNo` 回填图片、SKU 名和品类名。

## 在主链中的作用
- `goodsrecyclebill`
  - 主单执行成品库存扣减、原料库存增加。
  - 明细层则承接“转了哪些成品、多少件、多少金重”的事实记录。
- 报表页和导出页真正消耗的是明细层，而不是单头。

## 关键结论
- 成品转料明细不是薄子表，它同时承担了主单事实层和报表查询层两种职责。
- 若只看单头，会知道“转料发生了”；若要知道“转了什么、多少”，必须回到这张表。

## 风险与疑点
- `⚠️` `deleteGoodsRecycleDetail(...)` 当前是物理删除，未见状态限制。
- `⚠️` `listData(...)` 通过“明细列表 + 手工追加汇总行”的方式返回，调用方若不区分最后一行，容易把汇总当明细。
- `⚠️` `fillData(...)` 目前是空实现，说明部分补数字段已经迁到 mapper 或专门查询口径，后续改展示时要先分清事实来源。
