# 维修单详情
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修单详情不是独立审批单，而是维修域围绕 `wh_maintain_bill_detail` 的明细操作域。它一头承接维修单宿主链、维修入库单和维修出库单累计结果，另一头继续给单件入库单、单件调整单提供下推来源。

这意味着它在业务上承担三类角色：
1. 维修明细查询与导出视图
2. 维修流转状态维护入口
3. 维修终结与后续单据生成来源池

## 关键入口
- Controller: [MaintainBillDetailController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbilldetail/MaintainBillDetailController.java)
- Service: [MaintainBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainbilldetail/MaintainBillDetailServiceImpl.java)
- 终结入参: [MaintainFinalizationReqVO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbilldetail/vo/MaintainFinalizationReqVO.java)
- 明细实体: [MaintainBillDetailDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/maintainbilldetail/MaintainBillDetailDO.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_maintain_bill_detail` | 宿主明细表 | 维修全过程的明细落点，保存流转仓位、累计量、新旧件结果和终结信息 |
| `wh_maintain_bill` | 宿主单头 | 当所有明细终结后，单头 `replyStatus` 会被整体推进到完成 |
| `wh_maintain_in_bill_detail` | 最新返厂结果来源 | 查询页和下推单件入库/调整时，使用“最近一次维修入库明细”补齐当前结果 |
| `wh_item` | 单件主档 | 终结时会回写 `maintainBillDetailReplyStatus`；下推单件调整时会读取当前单件本体 |
| `wh_item_accessories` | 单件辅料 | 单件调整来源生成时会一并补齐辅料信息 |

## 查询与视图口径
### 1. 列表与导出
`getPage/getMaintainBillDetailList/getOptionPage` 都围绕 `maintainBillDetailMapper.selectPageData/selectListData` 展开，并补两类衍生数据：
1. `fillMaintainCmtFee(...)`
   取最近一次维修入库明细，补 `maintainItemCmtFee/maintainGramCmtFee`
2. `getMaintainBillDetailPageFillData(...)`
   按 `itemNo/newItemNo` 回填新旧单件卡片信息

导出时还会额外计算：
1. `inboundNumRest = num - inboundNum`
2. `finishNumRest = num - finishNum`
3. `finishProcessStr = finishNum / num`

因此“维修单详情”页本质是维修跟踪视图，不只是简单明细列表。

### 2. 流转仓位维护
`updateStepLocal(...)` 会把选中明细的：
1. `stepWareId/stepWare`
2. `stepLocal`
3. 特定条件下的 `replyStatus`

一起更新。这里有一个硬编码分支：当目标仓库名称等于 `"编货仓"` 时，系统直接把明细 `replyStatus` 改成 `"6"`，也就是把“进入编货仓”视为一条显式进度状态。

### 3. 调拨累计维护
`updateTransferNum(...)` 不是建单接口，而是维修详情侧的累计字段维护器：
1. `add = true` 时累加 `transferNum`
2. `add = false` 时扣减 `transferNum`
3. 非 `single` 模式下强校验 `0 <= transferNum <= num`

这说明维修明细自身就是多个后续执行链共享的数量基线。

## 终结链路
### 1. 入口
终结接口是：
- `PUT /wh/maintain-bill-detail/finalization`
- `MaintainBillDetailServiceImpl.finalization(reqVO, operator)`

入参只有两项：
1. `mdIds`
2. `reason`

### 2. 明细终结动作
`finalization(...)` 会对选中的每条维修明细执行：
1. 校验 `mdIds` 非空
2. 查询明细
3. 拦截已是 `COMPLETED` 的明细
4. 设置
   - `replyStatus = COMPLETED`
   - `finalizationTime = now`
   - `finalizationReason = reqVO.reason`
   - `finalizationMan = operator`
5. 把
   - `outNum = max(num, outNum)`
   - `inNum = max(num, outNum)`

也就是说，终结不是纯状态标记，而是把该明细的出厂/回厂累计量强制补齐到“至少等于应修数量”。

### 3. 单件侧回写
终结后，如果这些维修明细带 `itemId`，系统会统一回写：
1. `wh_item.maintainBillDetailReplyStatus = COMPLETED`

所以维修单详情是单件主档上“该维修明细已终结”这一状态的直接来源。

### 4. 维修单头完成判定
终结动作不会无条件把维修单头标完成，而是：
1. 把受影响 `billId` 全部取回
2. 重新加载这些单头下所有明细
3. 只有当“该维修单下全部明细都已 `COMPLETED`”时
4. 才把 `wh_maintain_bill.replyStatus = COMPLETED`

因此维修单详情是维修域的最终收口点之一，但仍然坚持“明细全完结，单头才完结”的聚合规则。

## 下游派生能力
### 1. 生成单件入库明细来源
`createItemEntryDetailVOByIds(...)` 会把维修明细转成单件入库草稿来源，关键口径是：
1. 基础款式、规格、图片来自 `sku`
2. `maintainBillNo` 来自维修单头
3. 当前返厂结果优先取最近一次 `MaintainInBillDetail`
4. 若存在最近返厂结果，则把
   - `goldWeight/grossWeight/weight`
   - `costGramCmtFee/costItemCmtFee`
   - `maintainGramCmtFee/maintainItemCmtFee`
   带到下游单件入库草稿

### 2. 生成单件调整明细来源
`createItemAdjustDetailVOByIds(...)` 会把维修明细转成单件调整草稿来源，读取口径是：
1. 当前旧件本体来自 `wh_item`
2. 辅料来自 `wh_item_accessories`
3. 维修工费显示优先取最近一次维修入库明细
4. 同一 `itemNo` 不允许重复引用

这说明维修单详情不是终点页，它还是单件返修后重新入库/改单的重要来源池。

## 当前结论
1. 维修单详情不是孤立查询页，而是维修域明细宿主模块。
2. `finalization(...)` 是维修域真正的“终结”入口，会同时改明细终结态、单件维修态，并在满足条件时把维修单头推进到完成。
3. 单件入库单和单件调整单的来源并不直接取维修单头，而是从维修明细结合“最近一次维修入库结果”生成。
4. 维修流转中的 `stepWare/stepLocal/replyStatus/transferNum` 都集中沉在这张明细表里，后续改维修域时不能只看单头。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `finalization` | 维修单详情 / 维修单 | 终结只拦“已完成”明细，没看到对“是否已真正走完整个维修链”的前置校验，理论上可把未完全流转的明细强制终结 | 待确认 | 确认终结前是否应补 `replyStatus` 白名单和累计量校验 |
| ⚠️ | `finalization` | 维修单详情 | 终结会把 `outNum/inNum` 直接补成至少等于 `num`，可能掩盖真实未闭环数量 | 待确认 | 确认业务上是否允许“终结即补齐累计量” |
| ⚠️ | `updateStepLocal` | 维修单详情 | 以仓库名称 `"编货仓"` 文案判断并把 `replyStatus` 写成 `"6"`，属于脆弱文本耦合 | 待确认 | 确认是否应改成稳定仓库编码或枚举判断 |
| ⚠️ | `createItemEntryDetailVOByIds` / `createItemAdjustDetailVOByIds` | 维修单详情 / 单件链 | 下推单件入库和调整时都依赖“最近一次维修入库明细”作为当前结果基础，若最近一笔缺失或滞后，会把旧结果继续下传 | 待确认 | 继续结合维修报表、单件入库和单件调整链核对“最近一次维修入库”是否总是可信 |
