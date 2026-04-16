# 单件销售

## 模块定位
- 入口：`ItemSaleServiceImpl`
- 宿主表：`wh_item_sale`
- 这是单件销售落点卫星层。

## 关键入口
- `insertBatch(...)`
- `removeByItemIds(...)`
- `getByEpc(...)`

## 真实行为
- service 很薄，没有独立 controller，说明它主要被上游销售链直接调用。
- `insertBatch(...)` 负责批量写入单件销售记录。
- `removeByItemIds(...)` 支持按单件 ID 集合整批清理销售落点。
- `getByEpc(...)` 支持按 EPC 反查单件销售记录。

## 在主链中的作用
- 给销售链补一层“单件已经卖出/被哪个销售动作消费”的落点记录。
- 作为 `item`、`salebill` 等上游主链之外的轻量事实层。

## 关键结论
- 这层虽然薄，但它是单件销售事实的专用宿主，不建议把它当普通缓存表看待。

## 风险与待确认
- `⚠️` 当前没有独立 controller 和状态流，完整写入时机完全依赖上游销售链一次性写对。
- `⚠️` `removeByItemIds(...)` 是物理删除，若未来要做销售历史追溯，需要确认是否还有别的审计链兜底。
