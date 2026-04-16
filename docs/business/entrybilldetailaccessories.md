# 单件入库明细辅石

## 模块定位
- 入口：`EntryBillDetailAccessoriesServiceImpl`
- 宿主表：`wh_entry_bill_detail_accessories`
- 这是单件入库明细侧的辅石快照层。

## 关键入口
- 该 service 当前仅提供基础继承能力，没有额外业务方法。

## 真实行为
- 它把入库时每条明细上的辅石信息独立快照下来。
- 字段与正式 `wh_item_accessories` 高度镜像，但额外保留：
  - `billId`
  - `detailId`
  - `semisSkuNo`
  - `semisPartNo`
  - `semisSkuName`
- 说明它不只是普通辅石列表，还带“从哪个入库明细、哪个半成品来源过来”的来源痕迹。

## 在主链中的作用
- 给单件入库单保留辅石原始录入快照。
- 作为后续生成正式单件辅石事实的前置来源池。

## 风险与待确认
- `⚠️` 当前 service 很薄，完整写入/清理时机完全依赖单件入库主链。
- `⚠️` 与正式 `itemaccessories` 字段高度镜像，后续若只改一边字段定义，容易出现快照层和结果层漂移。
