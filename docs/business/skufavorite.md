# 款式收藏模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skufavorite` 是 SKU 收藏宿主链，不只是关系表。它同时承接收藏标签、收藏切换和按标签统计收藏数。

## 关键入口
- Service: [SkuFavoriteServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skufavorite/SkuFavoriteServiceImpl.java)

## 真实行为
- `favorite(skuFavorite)`
  - 已收藏则取消收藏，未收藏则新增，实际是 toggle 语义。
- `createTag(tag)`
  - 同用户下标签名唯一。
- `hideTag(tag)`
  - 删除标签时会连带删除该标签下全部收藏。
- `getTagList(userId)`
  - 通过 join + count 输出标签及收藏数。

## 风险与待确认
1. `⚠️` `favorite(...)` 是 toggle 语义，不是幂等“只新增”。
2. `⚠️` `hideTag(...)` 会物理删标签和标签下全部收藏。
