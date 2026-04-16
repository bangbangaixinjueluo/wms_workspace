# 款式编码规则日志模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`skurulelog` 是 SKU 编码落号的历史轨迹表，不是普通日志。规则主表负责分配号码，这里负责保留“某个 SKU 最终用了什么编码”的历史。

## 关键入口
- Service: [SkuRuleLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skurulelog/SkuRuleLogServiceImpl.java)

## 真实行为
- `getSkuRuleLogBySkuNo(skuNo)`
  - 按 SKU 号查编码日志。
- `getSkuRuleLogVOPage(...)`
  - 提供编码日志分页视图。

## 在主链中的作用
- `skurule`
  - 规则主表负责分配 serialNo；日志表负责保留最终落号轨迹。

## 风险与待确认
1. `⚠️` `deleteSkuRuleLog(...)` 当前仍是物理删除，历史编码轨迹可被直接抹掉。
2. `⚠️` 服务层很薄，真正何时落日志强依赖调用方，不在这层保证。
