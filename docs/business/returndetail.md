# 客户退货单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`returndetail` 不是薄子表，而是客户退货链的查询宿主。退货行明细、来源单头、仓库、门店、客户、片区、材质、SKU、品类和权限过滤，最终都在这层拼成报表结果。

## 关键入口
- Service: [ReturnDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/returndetail/ReturnDetailServiceImpl.java)

## 真实行为
- `getReturnDetailVOPage(...)`
  - 分页时会把 `returnbill + warehouse + shop + customer + businessArea + texture + sku + category` 整组补齐。
- `getReturnDetailVOList(reqVO)`
  - 导出会补客户类型、业务类型、退货类型文本，并在末尾追加总计行。
- `insertBatch(detailDOS)`
  - 主单建单/改单时整批写入退货明细。
- `filterPerm(...) / filterPermByOptions(...)`
  - 退货明细查询不是裸查，还叠加了片区、仓库、用户权限过滤。

## 在主链中的作用
- `returnbill`
  - 退货单主链的行级查询、导出和权限可见性都依赖这层，而不是只看主单。

## 风险与待确认
1. `⚠️` `deleteReturnDetail(...)` 当前是物理删除，未见主单状态保护。
2. `⚠️` 导出列表会追加总计行，消费侧需要区分事实行与汇总行。
3. `⚠️` `fillData(resp)` 当前几乎为空，真正补数逻辑分散在分页/导出路径里，后续扩字段很容易出现不同接口口径不一致。
