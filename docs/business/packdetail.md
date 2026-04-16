# 产品包明细

## 模块定位
- 对应表：`wh_pack_detail`
- 入口：`PackDetailController`、`PackDetailServiceImpl`
- 这不是独立执行单，而是产品包的真实内容宿主；产品包单头更多是汇总和状态，真正包进了哪些 EPC、重量和工费都在这里。

## 主数据职责
- 关键字段包括：
  - `packId/packNo`
  - `trayNo`
  - `customerOrderNo/orderItemId`
  - `epc/itemNo`
  - `skuId/skuNo/modelNo/specs`
  - `num/goldWeight/weight/grossWeight/grossWeightRepeat`
  - `basicGramCmtFee/moreGramCmtFee/moreItemCmtFee/saleCmtFee`
  - `marking`
- 这张表同时承载“包内成员 + 小包归组 + 工费快照 + 字印需求标记”。

## 主要业务动作
- `createPackDetail(...)`
  - 新增单条产品包明细。
- `updatePackDetail(...)`
  - 修改单条明细。
- `deletePackDetail(...)`
  - 当前是物理删除。
- `deletePackDetailByPackId(packId)`
  - 产品包改单/重建时会整包删明细后重插。
- `insertBatch(...)`
  - 产品包建单和改单的主要落库入口之一。

## 查询与展示口径
- `getPackDetailPage(...)`
  - 不是单纯单表分页。
  - 先做 `filterPre(...)`：
    - 业务员按客户权限收缩 `customerIds`
    - 柜员/柜长再按仓库权限收缩 `wareId`
  - 再走 mapper 自定义分页和汇总。
- `exportPackDetailExcel(...)`
  - 会额外补：
    - 仓库名
    - 网点名
    - 包状态
    - 调拨状态
  - 末尾还会追加一行汇总。

## 在主链中的作用
- `pack`
  - 建包/改单时整包重建明细。
  - 审核时按明细校验：
    - 单件包的小包复核毛重
    - 成品包行级复核毛重
- `salebill`
  - 会按 `trayNo` 或包明细继续承接销售结算。
- `markingbill`
  - `getNeedMarkingsByPackId(...)` 直接从这里筛出需要字印的明细。

## 关键结论
- 产品包明细不是普通子表，而是产品包执行域最核心的事实表。
- 产品包后续能否结算、能否字印、能否按小包导出，最终都要落回这张表看明细字段。

## 风险与疑点
- `⚠️` `deletePackDetail(...)` 当前是物理删除，未见状态限制。
- `⚠️` `filterPre(...)` 与旧的 `handleParams(...)` 权限口径存在并行痕迹，后续查询权限改造要注意不要只改一边。
- `⚠️` `fillList(...)` 当前只稳定回填了 SKU 侧大类和材质，包头维度字段补数逻辑有部分注释残留，说明展示口径可能经历过迁移。
