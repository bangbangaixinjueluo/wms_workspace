# 结算方式
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
结算方式不是业务单据，也不是审核执行链，而是结算主数据表 `wh_settlement`。它负责定义各业务结算明细里可选的：
1. 结算编码 `settleNo`
2. 结算名称 `settleMethod`
3. 结算类型 `settleType`
4. 结算类别 `settleClass`
5. 所属事务类型 `bizType`

前面分析过的销售结算、退货结算、来料结算、维修结算，最终都要引用这里的编码或名称。

## 关键入口
- Controller: [SettlementController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/settlement/SettlementController.java)
- Service: [SettlementServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/settlement/SettlementServiceImpl.java)
- DO: [SettlementDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/settlement/SettlementDO.java)

## 数据结构
表：`wh_settlement`

关键字段：
1. `settleNo`：结算编码。
2. `settleMethod`：结算方式名称。
3. `settleType`：结算类型，代码注释里可见 `转欠/结价/工费/费用`。
4. `settleClass`：结算类别。
5. `preferPrice`：优惠单价。
6. `useTip`：使用说明。
7. `remark`：备注。
8. `bizType`：事务类型。
9. `sort`：排序。

## 模块真实行为
### 1. 只有主数据 CRUD
当前对外接口就是：
1. `create`
2. `update`
3. `delete`
4. `get`
5. `list`
6. `page`
7. `export-excel`

没有审核、反审、作废、流程审批，也不直接回写钱包、库存或单据状态。

### 2. 唯一性与组合校验
`createSettlement(...)` 会做两层校验：
1. 同 `settleNo + bizType` 不能重复。
2. 同 `settleType + bizType + settleMethod + settleClass + deleted=0` 不能重复。

`updateSettlement(...)` 也会校验同一 `settleNo + bizType` 是否与别的记录冲突。

这说明它是“事务类型下的结算方式字典”。

### 3. 被业务结算明细读取
虽然它自己不处理业务账，但 service 明确提供：
1. `getSettlementBySettleNo(...)`
2. `getSettlementListByNos(...)`
3. `getMapByNos(...)`
4. `getMapByNames(...)`
5. `checkSettleType(...)`

这些能力会被销售、退货、来料等业务结算明细层调用，用来校验结算编码是否合法、补充主数据含义。

## 关键业务口径
### 1. `bizType` 是分域字典
`getSettlementBySettleNo(settleNo, bizType)` 明确按事务类型取字典。当前注释可见至少覆盖：
1. `01` 销售结算
2. `02` 销售退货
3. `03` 客商来料
4. `04` 客商出料

所以同一个 `settleNo` 理论上是可以在不同事务类型下各自定义的。

### 2. 转欠/结价是硬性关键类型
`checkSettleType(Set<String> saleSettleNos)` 当前强制要求：
1. `RECIRCULATION`
2. `CLOSING_PRICE`

至少包含一个。

这意味着结算方式主数据虽然只是字典，但它已经被业务规则硬编码依赖，尤其是“转欠/结价”两个编码。

### 3. 业务层有时按编码，有时按名称
从前面已经分析过的业务链看：
1. 有些模块按 `settleNo` 稳定识别，例如维修确认里的 `CLOSING_PRICE`
2. 也有些旧逻辑按 `settleName/settleMethod` 文案识别

因此 `wh_settlement` 是很多结算逻辑的统一源头，但调用方口径并不完全统一。

## 风险与待确认
1. `deleteSettlement(Long id)` 是物理删除，未见“被业务结算明细引用时禁止删除”的保护。
2. `updateSettlement(...)` 当前只校验 `settleNo + bizType` 冲突，未重新做 create 时那种整组组合唯一校验。
3. `checkSettleType(...)` 只认可 `RECIRCULATION/CLOSING_PRICE`，说明业务层对这两个编码有硬依赖，主数据改编码风险很高。
4. 业务代码里既有按 `settleNo` 识别，也有按 `settleMethod` 文案识别；即使主数据本身稳定，调用方口径仍可能不一致。

## 结论
结算方式模块应当被定位为“结算主数据字典”，不是业务单据流。它本身不产生库存和资金动作，但很多业务结算明细、结价冲回、转欠规则都直接依赖它的编码与名称。后续如果改结算编码、事务类型、结算类别，影响面会跨销售、退货、来料、维修等多条财务链。
