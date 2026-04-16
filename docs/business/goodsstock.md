# 成品库存

## 1. 模块定位

- 对应表：`wh_goods_stock`
- 入口：`GoodsStockController`、`GoodsStockServiceImpl`
- 角色：成品库存结果表 + 库存更新宿主

## 2. 主链结论

- 它不是纯报表表，真正库存变更入口在 `doUpdateGoodsStock(...)`
- 成品库存日志由库存宿主统一生成
- 更新时按 `(shopId, wareId, skuId)` 聚合

## 3. 核心职责

- 查询成品库存并补 SKU 信息
- 校验库存是否充足
- 按单据正负方向更新库存数量/金重/重量/毛重
- 生成 `GoodsStockLogDO`

## 4. 代码结论

- `getGoodsStockPage(...)` 会把 `skuName` 先转成 `skuIds` 再查库存
- `doUpdateGoodsStock(...)`：
  - 不存在库存则插入；
  - 存在则按 `timestamp` 乐观锁更新；
  - 每次都批量写入库存日志
- 数量不足校验目前更多依赖金重，件数校验代码有注释痕迹

## 5. 关注点

- `⚠️` 件数库存不足校验当前有注释痕迹，真实约束边界需要确认
- `⚠️` `timestamp` 乐观锁是关键并发保护，修改前要整链回归
- `⚠️` service 既是查询层又是更新宿主，不能按普通报表 service 对待
