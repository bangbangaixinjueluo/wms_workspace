# 订单核销明细

## 模块定位
- 对应表：`wh_order_writeoff_detail`
- 入口：`OrderWriteoffDetailController`、`OrderWriteoffDetailServiceImpl`
- 这不是独立执行单，而是订单核销单的明细宿主，同时承接核销明细报表与“转成品调拨引用页”查询。

## 主数据职责
- 关键字段包括：
  - `orderId`
  - `demandDetailId`
  - `skuId`
  - `writeoffNum`
  - 各类截止累计、核销数量字段
- 它既记录“本次核销了多少”，也被拿来与截止累计一起重算“总已核销量”。

## 主要业务动作
- `insertBatch(...)`
  - 核销单建单或执行时批量写入核销明细。
- `getByOrderId/getListByOrderIds`
  - 主单读取整单明细的标准入口。
- `deleteByOrderId(orderId)`
  - 主单重建或删除时按单头整批删除明细。

## 查询与报表口径
- `getDetailQueryPage(...)`
  - 走自定义 SQL 分页，不是代码生成分页。
  - 会把：
    - `cutoffWriteoffSum`
    - `writeoffNum`
    - `demandDetail.customerSkuNo`
    组合成报表展示。
  - `totalWriteoffNum = cutoffWriteoffSum + writeoffNum`
- `getDetailQueryExcel(...)`
  - 导出直接走专门 SQL。
- `/page-to-goods-transfer`
  - 不是普通核销分页。
  - 会继续反查成品 SKU，把可转成品调拨的数据补成另一种引用视图。

## 在主链中的作用
- `orderwriteoff`
  - 单头承接审核/反审和回写。
  - 明细层承接“核销到了哪条需求、哪条 SKU、多少数量”。
- `goods transfer`
  - 明细页还被成品调拨侧拿去做来源池。

## 关键结论
- 订单核销明细不是普通子表，而是“核销事实层 + 报表层 + 调拨引用层”三合一明细宿主。
- 如果只看单头，只知道核销发生了；要知道核销到了哪条需求明细、累计核销了多少，必须回这张表。

## 风险与疑点
- `⚠️` `deleteByOrderId(...)` 当前是整批物理删除，未见额外状态保护。
- `⚠️` `getDetailQueryPage(...)` 里的 `totalWriteoffNum` 依赖“截止累计 + 本次核销量”现场重算，后续如果两个口径任何一边变了，报表就会飘。
- `⚠️` `/page-to-goods-transfer` 会过滤掉没有 `goodsSkuNo` 的记录，引用页看到的并不是全量核销明细。
