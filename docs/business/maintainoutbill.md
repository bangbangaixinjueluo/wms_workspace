# 维修出库单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修出库单对应维修链里的“送厂维修出库”执行单。它不是普通出库单，而是同时承担四类动作：
1. 把在库单件或成品转成维修态/维修出库态
2. 把返厂维修对应的材质重量和工费从供应商钱包扣走
3. 把维修单明细上的“最近一次入库结果”清空，推进到继续维修或 QC 返厂
4. 把成品退厂登记池的占用状态推进到已出厂

所以它本质上是“维修执行出库 + 供应商扣账 + 维修宿主链重置 + 退厂登记池推进”的复合执行单。

## 关键入口
- Controller: [MaintainOutBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainoutbill/MaintainOutBillController.java)
- Service: [MaintainOutBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainoutbill/MaintainOutBillServiceImpl.java)
- 审核业务层: [MaintainOutBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainOutBillBusiness.java)
- 建单辅助: [MaintainOutBillCreateBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainOutBillCreateBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_maintain_out_bill` | 主表 | 维修出库单头，保存审核态、供应商、来源退厂登记单与累计入库数 |
| `wh_maintain_out_bill_detail` | 明细表 | 维修出库明细，挂维修单明细与退厂登记明细 |
| `wh_maintain_bill` | 宿主表 | 审核/反审后重算维修单头进度 |
| `wh_maintain_bill_detail` | 宿主明细 | 回写 `outNum/replyStatus`，并清空/恢复最近一次入库结果 |
| `wh_goods_reject_record` | 退厂登记单头 | 审核/反审时推进 `outOfWare` 状态 |
| `wh_goods_reject_record_detail` | 退厂登记明细 | 审核/反审时增减 `maintainOutNum` |
| `wh_maintain_in_bill_detail` | 下游入库明细 | 草稿下推和作废时解除 `pushMaintainOutBillDetailId` |
| `wh_item` | 单件主档 | 审核时改成 `MAINTAIN`，反审时恢复 `ON_WARE` |
| `wh_item_log` | 单件日志 | 记录维修出库审核/反审的单件轨迹 |
| `wh_sku_stock` | 成品库存 | 审核出库、反审回库 |
| `wh_wallet` / `wh_wallet_log` | 供应商钱包 | 审核扣减，反审加回 |

## 建单逻辑
### 1. 合计值重算
`summaryCompute(reqVO)` 会重算：
1. `totalNum`
2. `totalGoldWeight`
3. `totalGrossWeight`
4. `totalWeight`
5. `totalCmtFee`
6. `totalSettleWeight`
7. `totalSettleMoney`

因此维修出库单头的工费和结算口径也是后端重算值，不完全信任前端传参。

### 2. 来源聚合
`DetailRelateData` 会把维修出库明细聚合到三条来源线上：
1. 单件或 SKU 本体
2. 成品退厂登记明细/单头
3. 维修单明细/维修单头

同时强约束：
1. 单件和成品不能同单混出
2. 必须能追到维修单明细
3. 若明细没直接带维修单明细，会从退厂登记明细里的 `maintainBillDetailId` 兜底回推

### 3. 草稿阶段的来源占用
`updateRelateData(detailList)` 在保存草稿后就会做两类挂接：
1. 退厂登记单头 `outOfWare = OUTING`
2. 若存在上游维修入库明细，则回写其 `pushMaintainOutBillDetailId`

这说明维修出库单草稿建立后，就已经开始占用退厂登记池和上游维修入库下推关系。

### 4. 出库单头补齐
`detailAfterUpdate(...)` 会按明细结果回写单头：
1. `bizType = 8001/8002`
2. `recordId/recordNo`
3. `maintainBillId/maintainBillNo`

所以维修出库单本身就是从维修单与退厂登记池两边汇总出来的执行单。

## 审核链
审核入口走：
1. `MaintainOutBillBusiness.confirm(id)`
2. `ConfirmHandle.handle()`

固定顺序是：
1. `preCheck()`
2. `itemHandle()`
3. `goodsHandle()`
4. `settleHandle()`
5. `billHandle()`
6. `relateBillHandle()`

### 1. 单件转维修态
`itemHandle()` 对单件维修出库会：
1. 把单件 `itemStatus` 改成 `MAINTAIN`
2. 把 `lockStatus` 改成锁定
3. 写 `wh_item_log`
4. `skuStockService.decreaseStock(...)` 扣减单件库存

所以维修出库审核的真实含义是“在库单件离库并进入维修态”。

### 2. 成品库存扣减
`goodsHandle()` 会统一调用：
`updateGoodsStock(goodsStockList, false, OUT, bizType)`

即不论单件维修出库还是成品维修出库，审核都把相应成品库存从账面扣掉。

### 3. 供应商钱包扣减
`settleHandle()` 审核调用：
`createWalletBaseDTO(DEBT, CONFIRM)`

钱包数据来自 `getWalletData()`，固定拆成两笔：
1. 材质钱包：按明细 `goldWeight` 合计扣减
2. 现金钱包：按明细 `cmtFee` 合计扣减，四舍五入到整数

也就是说，送厂维修出库时，系统把预计返厂维修对应的材质和工费先从供应商钱包扣出去。

### 4. 退厂登记池推进
`relateBillHandle()` 里若存在 `rejectRecordIds`，会：
1. 把退厂登记单头 `outOfWare = OUTED`
2. 把登记明细 `maintainOutNum += num`
3. 校验累计维修出库数不能超过登记数量

所以维修出库单是退厂登记池的真正执行单。

### 5. 维修单明细重置
`maintainOutNumHandle(...)` 是维修出库最关键的宿主回写逻辑。审核时它会：
1. 维修单明细 `outNum += num`
2. 备份当前“最近一次维修入库结果”到出库明细：
   - `inboundNumBak`
   - `repareFeeBak`
   - `newGoldWeightBak/newGrossWeightBak/newWeightBak`
   - `newItemIdBak/newItemNoBak`
3. 清空维修单明细上的最近一次入库结果：
   - `inboundNum = 0`
   - `repareFee = 0`
   - `newGoldWeight/newGrossWeight/newWeight = 0`
   - `newItemId/newItemNo = 0/空`
   - `lastGoldWeight/lastGrossWeight/lastWeight = 0`
4. `replyStatus` 切回 `REPAIRING`
5. 若同一单件存在再次维修出库记录，则改成 `QC_RETURN`
6. 最后重算维修单头进度

这说明维修出库单会主动清掉“上一轮维修入库成果”，把维修链重新打开。

## 反审链
维修出库反审走：
1. `MaintainOutBillBusiness.infirm(id)`
2. `InfirmHandle.handle()`

### 1. 单件恢复在库
反审时 `itemHandle()` 会：
1. 把单件状态改回 `ON_WARE`
2. 把锁状态改回未锁
3. 写反审日志
4. `skuStockService.increaseStock(...)` 回补单件库存

### 2. 成品库存回补
`goodsHandle()` 反审走的是库存镜像回滚，把审核时扣掉的成品库存加回来。

### 3. 供应商钱包回补
`settleHandle()` 改为：
`createWalletBaseDTO(DEPOSIT, INFIRM)`

即审核时扣掉的材质钱包和现金钱包整条加回。

### 4. 维修单明细恢复
反审时会按审核时备份到出库明细里的 `...Bak` 字段，把维修单明细恢复回去：
1. `outNum -= num`
2. `inboundNum/repareFee/newItem.../newWeight...` 恢复为出库前最近一次维修入库结果
3. `replyStatus` 按恢复后的口径回切
4. 再重算维修单头进度

### 5. 退厂登记池回滚
反审时退厂登记池会镜像回滚：
1. 退厂登记单头 `outOfWare` 恢复为 `NOT_OUT`
2. 登记明细 `maintainOutNum -= num`

## 反审前置限制
`InfirmHandle.preCheck()` 里能看到几条关键保护：
1. 单头必须已审核
2. `totalPushCount` 必须为空，否则视为“已经下推出库单据”，禁止反审
3. 跨日反审需要权限 `wh:maintain-out-bill:infirm`
4. `totalInNum` 必须等于 0，只要已有维修入库回收就不能反审
5. 若是单件，当前单件必须仍是 `MAINTAIN + LOCKED`

这说明维修出库单一旦被后续维修入库承接，就会严格锁死反审。

## 作废链
Service 侧存在作废/解除来源挂接的清理逻辑，核心是：
1. 解除退厂登记单头 `outOfWare`
2. 清理下游维修入库明细上的 `pushMaintainOutBillDetailId`
3. 重算被下推的维修入库单头 `totalPushCount`

从代码结构看，维修出库单作废不会直接改库存和钱包，但会解除草稿态的来源/下推占用关系。

## 当前结论
1. 维修出库单不是普通送修单，而是把在库货正式切成维修态并扣减供应商钱包的执行单。
2. 审核最关键的不只是出库和扣账，而是会清空维修单明细上的最近一次入库结果，把维修流程重新打开。
3. 反审必须在“未被维修入库回收、未再被下推”的前提下才允许做整条回滚。
4. 退厂登记池是维修出库单的重要前置来源，草稿和审核两个阶段都会持续回写它。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `updateRelateData` | 维修出库单 / 退厂登记池 | 草稿保存阶段就会把退厂登记单头改成 `OUTING`，不是审核后才推进 | 待确认 | 确认业务上是否允许“草稿即占用退厂池” |
| ⚠️ | `maintainOutNumHandle` | 维修出库单 / 维修单 | 审核时会把维修单明细上一轮入库结果整组清空，维护时很容易误伤 `newItem.../last...` 口径 | 待确认 | 后续改造必须把 `...Bak` 备份恢复链一并回归 |
| ⚠️ | `preCheck` | 维修出库单 | 反审强依赖 `totalInNum = 0` 与 `totalPushCount = 0`，只要后续已有维修入库或下推链就彻底锁死 | 待确认 | 分析维修详情时继续核对还有没有别的下游占用入口 |
| ⚠️ | `settleHandle` | 维修出库单 | 钱包口径是“材质重量 + 工费金额”一起扣减，若后续只改其中一边会造成供应商账不平 | 待确认 | 涉及钱包改动时必须同时回归材质钱包和现金钱包两条账本 |
