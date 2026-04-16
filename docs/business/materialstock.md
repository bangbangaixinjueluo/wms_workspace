# 原料库存

## 1. 模块定位

- 对应表：`wh_material_stock`、`wh_material_stock_daily`、`wh_material_stock_daily_diff`
- 入口：`MaterialStockController`、`MaterialStockServiceImpl`
- 角色：原料库存结果表 + 原料库存更新宿主 + 日报/差异宿主

## 2. 主链结论

- 原料库存不是纯查询表，真实更新入口是 `updateMaterialStock(MaterialStockDTO)`
- 它比成品库存更重，除了实时库存，还要维护原料日报、差异和告警链
- 更新时按 `materialNo + shopId + wareId` 聚合

## 3. 核心职责

- 查询原料库存并补网点、仓库、原料名
- 按单据正负方向更新库存余额和毛重
- 生成 `MaterialStockLogDO`
- 维护 `material_stock_daily`
- 在日报差异链里支持差异记录与告警

## 4. 代码结论

- `updateMaterialStock(MaterialStockDTO)`：
  - 以 `baseStockDO + baseStockLogDO + materialNoAndWeight` 为宿主；
  - 支持 `BizTypeEnum.G_RECYCLE` 这种允许负向口径的特殊业务；
  - 插入或更新库存后统一写日志；
  - 每种原料都会调用 `refreshDaily(...)`
- `refreshDaily(...)`：
  - 只处理非当日、非未来、非零差异；
  - 只允许回刷近 3 个月；
  - 使用 Redisson 锁串行化

## 5. 关注点

- `⚠️ deleteMaterialStock(...)` 是物理删除，未见对原料日志、日报和历史单据保护
- `⚠️ refreshDaily(...)` 是原料链的重要一致性节点，改日报口径前要整链回归
- `⚠️` service 里同时接了差异表、告警邮件和 Redisson 锁，说明它已经是原料库存域宿主，不是单纯列表服务
