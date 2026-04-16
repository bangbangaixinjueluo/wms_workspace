# 维修出库单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`maintainoutbilldetail` 不是普通子表，而是维修出库执行事实层。重复维修检测、后续是否还有维修动作、单件是否仍处于维修出库链里，都通过这层判断。

## 关键入口
- Service: [MaintainOutBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainoutbilldetail/MaintainOutBillDetailServiceImpl.java)

## 真实行为
- `findByMaintainBillDetailId(idList)`
  - 只查 `billStatus = "1"` 的已审核维修出库明细。
- `findAliveByItemNo(itemNoList)`
  - 查询仍处于维修出库链中的单件记录。
- `getAfterByMainDetailIds(mdIds, afterTime)`
  - 查询某时点之后的新维修出库明细，并过滤掉作废单 `billStatus = "3"`。
- `getMaintainAgainByItemId(itemIds, currIds)`
  - 判断单件是否已再次进入新的已审核维修出库单。
- `page(pageVO)`
  - 提供维修出库明细分页视图。

## 在主链中的作用
- `maintainoutbill`
  - 反审时要靠这层判断“是否已被后续维修入库承接/是否已再次送修”。
- `maintainbilldetail`
  - 重复维修、时序校验和后续链判断都依赖这层事实表。

## 风险与待确认
1. `⚠️` `removeByBillId(...)` 当前是物理删除，未见主单状态保护。
2. `⚠️` 多处状态判断直接硬编码 `"1"`、`"3"`，状态语义散落在字符串里。
3. `⚠️` “再次维修”与“仍存活维修出库记录”的判断都依赖明细层查询，若明细删除或状态漂移，会直接影响反审和重复维修判定。
