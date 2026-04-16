# 网点款式库

## 模块定位
- 入口：`SkuShopController`、`SkuShopServiceImpl`
- 宿主表：`wh_sku_shop`
- 这不是简单映射表，而是“SKU 与网点归属 + 网点附加工费 + 图片展示补数”的轻量宿主。

## 关键入口
- `/wh/sku-shop/create`
- `/wh/sku-shop/page`
- `fillData(SkuShopRespVO)`

## 真实行为
- 建关系时不是只插一条，而是遍历请求里的 `skuIds` 批量建关系。
- 建档时会从正式 `wh_sku` 复制：
  - `basicGramCmtFee -> moreGramCmtFee`
  - `basicItemCmtFee -> moreItemCmtFee`
- 页面展示时会补：
  - 网点名
  - `skuNo/skuName`
  - `fileApi` 图片列表与首图
- `/page` 支持先按 `skuNo` 反查正式 SKU，再转 `skuId` 分页。

## 在主链中的作用
- 承接“某款在哪些网点可用/适用”的配置。
- 给网点维度价格或工费扩展提供单独入口。

## 关键结论
- 它更像“SKU 网点索引 + 网点侧冗余费用层”，不是纯关系表。

## 风险与待确认
- `⚠️` `deleteSkuShop(...)` 仍是物理删除。
- `⚠️` 建档时把 SKU 基础工费复制进 `moreGramCmtFee/moreItemCmtFee`，字段语义看起来更像“额外工费”，需要确认是不是命名与实际口径不一致。
- `⚠️` 图片展示完全依赖正式 SKU 文件链，若文件侧调整，网点款式页会跟着变。
