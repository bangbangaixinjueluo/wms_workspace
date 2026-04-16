# 款式搜索日志模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skusearchlog` 是 SKU 搜索行为日志宿主，承接按用户昵称回溯搜索轨迹的能力。

## 关键入口
- Service: [SkuSearchLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skusearchlog/SkuSearchLogServiceImpl.java)

## 真实行为
- `getSkuSearchLogPage(pageReqVO)`
  - 若传了 `userNickname`，会先经 `AdminUserApi` 转成用户 id 集合，再分页查日志。

## 在主链中的作用
- `sku`
  - 作为款式查询行为审计的侧链存在。

## 风险与待确认
1. `⚠️` `validateSkuSearchLogExists(...)` 里异常被注释掉了，删除或更新不存在记录时不会明确报错。
2. `⚠️` `deleteSkuSearchLog(...)` 仍是物理删除。
