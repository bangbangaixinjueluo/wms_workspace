# 单件调整单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
单件调整单对应 `BillTypeEnum.SINGLE_ADJUSTMENT`，业务类型固定为：
1. `1301 = SG_ADJUSTMENT`

它不是删除旧单件再新建单件，而是“在原单件上做属性重写”的调整链，同时伴随两类库存差额调整：
1. 单件库存移动账
2. 成品库存差额调整

如果单件挂维修单，还会同步回写维修单明细。

## 关键入口
- Controller: [AdjustBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/adjustbill/AdjustBillController.java)
- Service: [AdjustBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/adjustbill/AdjustBillServiceImpl.java)
- Business: [AdjustBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/AdjustBillBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_adjust_bill` | 主表 | 单件调整单头 |
| `wh_adjust_bill_detail` | 明细表 | 单件调整行 |
| `wh_adjust_bill_detail_accessories` | 明细辅料 | 调整单辅料快照 |
| `wh_item` | 单件表 | 审批通过后直接更新原单件属性 |
| `wh_item_log` | 单件日志 | 审批通过后写一出一入两条日志 |
| `wh_item_accessories` | 单件辅料 | 审批通过后重建辅料 |
| `wh_sku_stock` | 库存表 | 承接单件库存 moveStock 与成品库存差额 |
| `wh_maintain_bill_detail` | 维修单明细 | 若挂维修明细则同步更新新件信息 |
| `wh_maintain_bill` | 维修单头 | 仅做状态保护与归属校验 |

## 建单与修改
### 1. 单头
建单时后端生成 `billNo`，并重算：
1. `totalNum`
2. `totalGoldWeight`

### 2. 明细校验
`AdjustBillBusiness.create()/modify()` 会校验：
1. `epc/itemNo` 必填
2. EPC 对应单件必须存在
3. 单件必须是 `ON_WARE` 或 `DISPLAY`
4. 单件必须 `UN_LOCK`
5. 若调整后 `epc/itemNo` 变化，必须保证新值在系统里唯一
6. 若挂维修单明细，单据内不能重复引用同一条维修明细

### 3. 模号与辅料
建单阶段会：
1. 根据 `editModelNo` 决定用手工模号还是标准模号
2. 必要时自动补 `SkuModel`
3. 计算工费、销售工费、总工费
4. 保存辅料快照到 `wh_adjust_bill_detail_accessories`

### 4. 预检查
`preCheck()` 还会额外提示：
1. 成本克工费是否大于等于销售克工费
2. 销售克工费是否超过 2 位数
3. 当前填写值是否高于款式库基准值

这说明它不是单纯 CRUD，而是带财务口径校验的单件改档入口。

## 审批链
### 1. 提交
`submit(Long id)` 会真实发起 BPM：
1. 创建流程实例
2. `processResult = PROCESS`
3. `billStatus = CONFIRMING`

所以它和单件入库不同，当前仍然是审批驱动模块。

### 2. 审批结果回写
`updateProcessResult(Long id, Integer result, String nickName)` 是真正落业务的入口。

无论通过还是驳回，都会先做两件事：
1. `dealMaintain(...)` 处理维修链
2. `unlockByEpcList(...)` 释放单件锁定

只有审批通过时，才继续做单件和库存改动。

### 3. 审批通过时的主链
审批通过后主顺序是：
1. 单头改成 `CONFIRM`
2. 逐条把调整后属性覆盖回 `wh_item`
3. 重建 `wh_item_accessories`
4. 写 `wh_item_log` 两条日志
5. 调整成品库存差额
6. 调整单件库存移动账

## 单件改写
审批通过不是“删旧建新”，而是直接更新原 `wh_item`：
1. `skuId/skuNo/modelNo/specs`
2. `bigClassId/textureId`
3. `goldWeight/weight`
4. 各类工费、图片、业务信息
5. 辅料口径

所以单件调整单本质上是“原单件主数据重写单”。

## 单件日志
审批通过时，每条明细都会生成一出一入两条日志：
1. `OUT`
   - 记录调整前单件口径
2. `IN`
   - 记录调整后单件口径

这两条日志共用同一张调整单号，只是重量、款式、模号分别取前后值。

## 库存口径
### 1. 单件库存
`skuStockService.moveStock(...)` 吃的是 `adjustList`，每条明细都会生成：
1. 一条旧单件 `buildOutOne(itemDO)`
2. 一条新单件 `buildInOne(...)`

也就是单件库存口径按“旧档出、新档入”理解。

### 2. 成品库存
`skuStockService.updateGoodsStockAdjust(goodsStockList, false, BizTypeEnum.getEnum(adjustBill.getBizType()))`

这里不是整件进出，而是按金重差额调成品库存：
1. 旧 10g 调成新 15g：扣减新款对应成品库存 5g
2. 旧 10g 调成新 5g：增加旧款对应成品库存 5g

代码里 `GoodsSkuStockBO.buildInOne(...)` 的 `num` 固定传 `0`，说明成品侧更强调重量差额，不强调件数差额。

## 维修链回写
`dealMaintain()` 会先收集 `maintainBillDetailId`；
如果明细里没带这个字段，还会尝试根据当前 `itemId` 去找“仍存活”的维修明细。

最终 `updateMaintainBill(...)` 会：
1. 把维修明细 `newGoldWeight/newGrossWeight/newWeight` 改成调整后值
2. 回写 `newItemId/newItemNo`
3. 若维修单或维修明细已完结，则只记录告警不再改

所以单件调整会直接改变维修单里“返修后新件”的最终口径。

## 作废与删除
### 作废
`discard(Long id)` 只允许：
1. `billStatus = INIT`
2. `processResult != PROCESS`
3. `processResult != APPROVE`

满足后才改成 `DISCARD`。

### 删除
`deleteAdjustBill(Long id)` 直接物理删除主表，再逐条删除明细；当前看不到状态限制。

## 当前结论
1. 单件调整单不是库存外的资料修改，而是“单件主数据 + 单件库存 + 成品库存差额 + 维修单新件信息”联动修改。
2. 它和单件入库最大的区别是：单件入库创建新单件，单件调整修改已有单件。
3. 成品库存侧不是整件流转，而是按“前后金重差额”做补扣。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteAdjustBill` | 单件调整单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | `discard` | 单件调整单 | 单据不存在时抛的是 `TRANSFER_BILL_NOT_EXISTS`，错误码串到调拨单模块 | 待确认 | 确认是否应改成调整单自己的错误码 |
| ⚠️ | `updateProcessResult` | 单件调整单 | 未通过审批时也会先执行 `dealMaintain()` 与 `unlockByEpcList()` | 待确认 | 确认驳回时提前释放锁和回写维修单是否符合预期 |
| ⚠️ | `updateGoodsStockAdjust` | 单件调整单 / 成品库存 | 成品库存差额只看重量差，`num` 固定为 `0` | 待确认 | 确认业务上是否明确“不按件数调成品库存” |
