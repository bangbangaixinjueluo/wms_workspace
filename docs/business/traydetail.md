# 小包明细

## 模块定位
- 对应表：`wh_tray_detail`
- 入口：`TrayDetailController`、`TrayDetailServiceImpl`
- 这不是独立执行单，而是小包的真实内容宿主；`tray` 只是头表，真正装了哪些 EPC、重量和工费都在这里。

## 主数据职责
- 关键字段包括：
  - `trayId`
  - `itemId`
  - `epc/itemNo`
  - `skuNo/skuName/categoryName`
  - `weight/goldWeight/grossWeight/tareWeight/grossWeightRepeat`
  - `basicGramCmtFee/basicItemCmtFee/moreGramCmtFee/moreItemCmtFee/saleCmtFee`
  - `modelNo`
  - `remark`
- 这张表同时承载“小包内成员 + 重量快照 + 工费快照”三组语义。

## 主要业务动作
- `createTrayDetail(...)`
  - 新增单条小包明细。
- `updateTrayDetail(...)`
  - 修改单条明细。
- `updateBatchTrayDetailDO(...)`
  - 批量更新是更常见入口，`tray.update-detail` 走的就是这条。
- `deleteTrayDetail(...)`
  - 当前是物理删除。
- `getTrayDetailListByTratid(trayId)`
  - 这是小包详情页最常用的读取入口。

## 上下游关系
- 上游：
  - `pack`
    - 建包/改单时生成和重建小包明细。
  - `customerorder-intent`
    - 现场配包也会生成小包明细。
- 下游：
  - `tray`
    - 展示时按明细再反查 `wh_item` 补实时重量和图片。
  - `salebill`
    - 会按 `trayNo` 找回产品包明细，再间接消费小包维度。

## 关键结论
- 小包明细不是薄子表，它是产品包里“包内 EPC 清单 + 工费快照 + 复核毛重基础”的真实宿主。
- 头表很多展示值最终都要从这张表出发，再叠加 `wh_item` 实时信息。

## 风险与疑点
- `⚠️` `deleteTrayDetail(...)` 当前是物理删除，未见状态限制。
- `⚠️` `tray.update-detail` 批量更新只改明细，未看到同步重算小包头合计的统一入口。
- `⚠️` 小包头展示值一部分来自明细快照，一部分来自 `wh_item` 实时反查，双口径需要持续注意。
