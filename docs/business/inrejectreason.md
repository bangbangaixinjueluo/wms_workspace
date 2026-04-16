# 入库/退厂原因模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`inrejectreason` 不是普通字典，而是成品入库、成品退厂两条报表链共同依赖的原因主数据宿主。

## 关键入口
- Service: [InRejectReasonServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/inrejectreason/InRejectReasonServiceImpl.java)

## 真实行为
- `getByCode(type, code)`
  - 按“原因类型 + 原因编码”返回单条原因。
- `getMap(type)`
  - 直接输出 `code -> reason` 映射，供报表层批量翻译。
- `getMapByCode(type, codes)`
  - 按指定编码集合输出映射。

## 在主链中的作用
- `goodsinboundbilldetail`
  - 入库报表按 `IN` 原因字典翻译原因。
- `goodsrejectbilldetail`
  - 退厂报表按 `REJECT` 原因字典翻译原因。

## 风险与待确认
1. `⚠️` `deleteInRejectReason(...)` 当前是物理删除，未见对历史报表引用的保护。
2. `⚠️` 原因字典已经直接参与报表翻译，改编码或改类型会立刻影响历史展示。
