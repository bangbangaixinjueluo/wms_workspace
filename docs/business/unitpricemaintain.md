# 单价维护单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`unitpricemaintain` 不是普通改单，而是“维护单头 + 明细校验 + 反写正式单价主档”的宿主链。

## 关键入口
- Service: [UnitPriceMaintainServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/unitpricemaintain/UnitPriceMaintainServiceImpl.java)

## 真实行为
- `createUnitPriceMaintain(...)`
  - 建单时会生成维护单号、校验明细唯一性与单价大于 0，并把明细同步回正式 `unitprice` 主表。
- `doMaintainUnitPrice(detailList)`
  - 若正式单价已存在则更新，不存在则创建。
- `updateUnitPriceMaintain(...)`
  - 改单时会删旧明细后重建，并同步维护正式单价主表。
- `fillData(resp, selectDetails)`
  - 明细回显时会拼出 `priceInfo`，并补部门名、创建人、更新人。

## 在主链中的作用
- `unitprice`
  - 正式单价主档由维护单驱动更新。

## 风险与待确认
1. `⚠️` `updateUnitPriceMaintain(...)` 采用删旧明细后重建模式。
2. `⚠️` 维护单和正式单价主档强耦合，改维护单即实时改正式价格，需要确认是否符合预期。
3. `⚠️` `deleteUnitPriceMaintain(...)` 只删维护单和维护明细，不会回滚已同步到正式单价的结果。
