# 会话交接页
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 1. 当前基准信息

- 当前阶段：核心交易链已完成，维修域已补到“维修单报表”
- 当前目标：继续沿委外链扩展，并收维修域剩余辅助模块
- 当前总入口：
  - [analysis-workflow.md](/D:/ws/code/wms-api/docs/business/analysis-workflow.md)
  - [overview.md](/D:/ws/code/wms-api/docs/business/overview.md)

## 2. 当前分析范围

当前已完成的核心交易链模块：
1. 客户订单
2. 客户订单 - 意向单
3. 客留存
4. 产品包
5. 调拨单
6. 成品调拨单
7. 订单需求单
8. 出库结算单
9. 出货单
10. 订单核销
11. 收款单
12. 付款单
13. 客户应收账调整
14. 供应商应付账调整
15. 调账单
16. 结价单
17. 盈亏调整单
18. 损耗单
19. 客户来料单
20. 供应商来料单
21. 成品入库单
22. 客户出料单
23. 供应商出料单
24. 客户退货单
25. 成品退厂单
26. 成品退厂登记单
27. 成品转料单
28. 单件入库单
29. 单件调整单
30. 单件作废单
31. 单件盘点单操作
32. 库位调整单
33. 单件明细
34. 盘点称重单
35. 维修单
36. 维修入库单
37. 维修出库单
38. 维修单详情
39. 维修单报表

当前范围内已同步补齐的产物：
1. 实体关系图
2. 状态机图
3. 跨模块回写清单
4. 外部系统交互清单
5. 字段语义表
6. 变更影响清单

## 3. 已完成产物

### 3.1 已完成模块文档
- [customerorder.md](/D:/ws/code/wms-api/docs/business/customerorder.md)
- [customerorder-intent.md](/D:/ws/code/wms-api/docs/business/customerorder-intent.md)
- [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md)
- [pack.md](/D:/ws/code/wms-api/docs/business/pack.md)
- [transferbill.md](/D:/ws/code/wms-api/docs/business/transferbill.md)
- [goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md)
- [demandorder.md](/D:/ws/code/wms-api/docs/business/demandorder.md)
- [salebill.md](/D:/ws/code/wms-api/docs/business/salebill.md)
- [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md)
- [orderwriteoff.md](/D:/ws/code/wms-api/docs/business/orderwriteoff.md)
- [receivablebill.md](/D:/ws/code/wms-api/docs/business/receivablebill.md)
- [payablebill.md](/D:/ws/code/wms-api/docs/business/payablebill.md)
- [customeradjustbill.md](/D:/ws/code/wms-api/docs/business/customeradjustbill.md)
- [supplieradjustbill.md](/D:/ws/code/wms-api/docs/business/supplieradjustbill.md)
- [accountadjustbill.md](/D:/ws/code/wms-api/docs/business/accountadjustbill.md)
- [closingprice.md](/D:/ws/code/wms-api/docs/business/closingprice.md)
- [profitlossadjustbill.md](/D:/ws/code/wms-api/docs/business/profitlossadjustbill.md)
- [wastagebill.md](/D:/ws/code/wms-api/docs/business/wastagebill.md)
- [materialinbound.md](/D:/ws/code/wms-api/docs/business/materialinbound.md)
- [suppliermaterialinbound.md](/D:/ws/code/wms-api/docs/business/suppliermaterialinbound.md)
- [goodsinboundbill.md](/D:/ws/code/wms-api/docs/business/goodsinboundbill.md)
- [materialoutbound.md](/D:/ws/code/wms-api/docs/business/materialoutbound.md)
- [suppliermaterialoutbound.md](/D:/ws/code/wms-api/docs/business/suppliermaterialoutbound.md)
- [returnbill.md](/D:/ws/code/wms-api/docs/business/returnbill.md)
- [goodsrejectbill.md](/D:/ws/code/wms-api/docs/business/goodsrejectbill.md)
- [goodsrejectrecord.md](/D:/ws/code/wms-api/docs/business/goodsrejectrecord.md)
- [goodsrecyclebill.md](/D:/ws/code/wms-api/docs/business/goodsrecyclebill.md)
- [entrybill.md](/D:/ws/code/wms-api/docs/business/entrybill.md)
- [adjustbill.md](/D:/ws/code/wms-api/docs/business/adjustbill.md)
- [cancelbill.md](/D:/ws/code/wms-api/docs/business/cancelbill.md)
- [iteminventorybill.md](/D:/ws/code/wms-api/docs/business/iteminventorybill.md)
- [shelvesbill.md](/D:/ws/code/wms-api/docs/business/shelvesbill.md)
- [item.md](/D:/ws/code/wms-api/docs/business/item.md)
- [inventoryweighingbill.md](/D:/ws/code/wms-api/docs/business/inventoryweighingbill.md)
- [maintainbill.md](/D:/ws/code/wms-api/docs/business/maintainbill.md)
- [maintaininbill.md](/D:/ws/code/wms-api/docs/business/maintaininbill.md)
- [maintainoutbill.md](/D:/ws/code/wms-api/docs/business/maintainoutbill.md)

### 3.2 已完成图表与总表
- [entity-overview.md](/D:/ws/code/wms-api/docs/business/model/entity-overview.md)
- [entity-customer-order-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-customer-order-chain.md)
- [entity-transfer-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-transfer-chain.md)
- [customer-order-state.md](/D:/ws/code/wms-api/docs/business/model/customer-order-state.md)
- [customerretain-state.md](/D:/ws/code/wms-api/docs/business/model/customerretain-state.md)
- [pack-state.md](/D:/ws/code/wms-api/docs/business/model/pack-state.md)
- [sale-bill-state.md](/D:/ws/code/wms-api/docs/business/model/sale-bill-state.md)
- [delivery-state.md](/D:/ws/code/wms-api/docs/business/model/delivery-state.md)
- [transfer-state.md](/D:/ws/code/wms-api/docs/business/model/transfer-state.md)
- [entity-writeoff-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-writeoff-chain.md)
- [order-writeoff-state.md](/D:/ws/code/wms-api/docs/business/model/order-writeoff-state.md)
- [entity-receivable-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-receivable-chain.md)
- [receivable-bill-state.md](/D:/ws/code/wms-api/docs/business/model/receivable-bill-state.md)
- [entity-payable-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-payable-chain.md)
- [payable-bill-state.md](/D:/ws/code/wms-api/docs/business/model/payable-bill-state.md)
- [entity-customer-adjust-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-customer-adjust-chain.md)
- [customer-adjust-bill-state.md](/D:/ws/code/wms-api/docs/business/model/customer-adjust-bill-state.md)
- [entity-supplier-adjust-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-supplier-adjust-chain.md)
- [supplier-adjust-bill-state.md](/D:/ws/code/wms-api/docs/business/model/supplier-adjust-bill-state.md)
- [entity-account-adjust-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-account-adjust-chain.md)
- [account-adjust-bill-state.md](/D:/ws/code/wms-api/docs/business/model/account-adjust-bill-state.md)
- [entity-closing-price-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-closing-price-chain.md)
- [closing-price-state.md](/D:/ws/code/wms-api/docs/business/model/closing-price-state.md)
- [entity-profit-loss-adjust-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-profit-loss-adjust-chain.md)
- [profit-loss-adjust-bill-state.md](/D:/ws/code/wms-api/docs/business/model/profit-loss-adjust-bill-state.md)
- [entity-wastage-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-wastage-chain.md)
- [wastage-bill-state.md](/D:/ws/code/wms-api/docs/business/model/wastage-bill-state.md)
- [entity-material-inbound-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-material-inbound-chain.md)
- [material-inbound-state.md](/D:/ws/code/wms-api/docs/business/model/material-inbound-state.md)
- [entity-supplier-material-inbound-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-supplier-material-inbound-chain.md)
- [supplier-material-inbound-state.md](/D:/ws/code/wms-api/docs/business/model/supplier-material-inbound-state.md)
- [entity-goods-inbound-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-goods-inbound-chain.md)
- [goods-inbound-bill-state.md](/D:/ws/code/wms-api/docs/business/model/goods-inbound-bill-state.md)
- [entity-material-outbound-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-material-outbound-chain.md)
- [material-outbound-state.md](/D:/ws/code/wms-api/docs/business/model/material-outbound-state.md)
- [entity-supplier-material-outbound-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-supplier-material-outbound-chain.md)
- [supplier-material-outbound-state.md](/D:/ws/code/wms-api/docs/business/model/supplier-material-outbound-state.md)
- [entity-return-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-return-chain.md)
- [return-bill-state.md](/D:/ws/code/wms-api/docs/business/model/return-bill-state.md)
- [entity-goods-reject-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-goods-reject-chain.md)
- [goods-reject-bill-state.md](/D:/ws/code/wms-api/docs/business/model/goods-reject-bill-state.md)
- [entity-goods-reject-record-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-goods-reject-record-chain.md)
- [goods-reject-record-state.md](/D:/ws/code/wms-api/docs/business/model/goods-reject-record-state.md)
- [entity-goods-recycle-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-goods-recycle-chain.md)
- [goods-recycle-bill-state.md](/D:/ws/code/wms-api/docs/business/model/goods-recycle-bill-state.md)
- [entity-entry-bill-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-entry-bill-chain.md)
- [entry-bill-state.md](/D:/ws/code/wms-api/docs/business/model/entry-bill-state.md)
- [entity-adjust-bill-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-adjust-bill-chain.md)
- [adjust-bill-state.md](/D:/ws/code/wms-api/docs/business/model/adjust-bill-state.md)
- [entity-cancel-bill-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-cancel-bill-chain.md)
- [cancel-bill-state.md](/D:/ws/code/wms-api/docs/business/model/cancel-bill-state.md)
- [entity-item-inventory-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-item-inventory-chain.md)
- [item-inventory-bill-state.md](/D:/ws/code/wms-api/docs/business/model/item-inventory-bill-state.md)
- [entity-shelves-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-shelves-chain.md)
- [shelves-bill-state.md](/D:/ws/code/wms-api/docs/business/model/shelves-bill-state.md)
- [entity-item-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-item-domain-chain.md)
- [item-entity-state.md](/D:/ws/code/wms-api/docs/business/model/item-entity-state.md)
- [entity-inventory-weighing-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-inventory-weighing-chain.md)
- [inventory-weighing-bill-state.md](/D:/ws/code/wms-api/docs/business/model/inventory-weighing-bill-state.md)
- [entity-maintain-bill-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-bill-chain.md)
- [maintain-bill-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-bill-state.md)
- [entity-maintain-in-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-in-chain.md)
- [maintain-in-bill-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-in-bill-state.md)
- [entity-maintain-out-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-out-chain.md)
- [maintain-out-bill-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-out-bill-state.md)
- [entity-maintain-detail-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-detail-chain.md)
- [maintain-bill-detail-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-bill-detail-state.md)
- [entity-maintain-report-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-report-chain.md)
- [maintain-bill-report-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-bill-report-state.md)
- [cross-module-writeback.md](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)
- [external-interactions.md](/D:/ws/code/wms-api/docs/business/external-interactions.md)
- [field-semantics.md](/D:/ws/code/wms-api/docs/business/field-semantics.md)
- [change-impact.md](/D:/ws/code/wms-api/docs/business/change-impact.md)

## 4. 当前状态判断

1. 当前没有“进行中但未落盘”的模块。
2. 当前主链可以视为已完成首轮闭环。
3. 订单核销模块已落盘，当前已明确它主要回写委外单和需求单，不直接承接出库结算单。
4. 收款单模块已落盘，当前已明确它是“银行余额 + 钱包余额”双账本驱动入口，且分账只重分配钱包归属。
5. 付款单模块已落盘，当前已明确它是收款单的镜像链路：审核扣银行卡、加钱包，反审反向回滚。
6. 客户应收账调整已落盘，当前已明确它只改客户钱包，不走银行卡，且金额与金重会分别落不同钱包口径。
7. 供应商应付账调整已落盘，当前已明确它是客户应收账调整的供应商镜像链：同样只改钱包，金额与金重分别落不同钱包口径。
8. 调账单已落盘，当前已明确它不是单边调整，而是调出账户/调入账户成对过账，金额链和金重链各自做双分录。
9. 结价单已落盘，当前已明确它不是独立主表，而是宿主单据里的 `CLOSING_PRICE(02)` 结算分支：先并入主账，再做一笔反向结价冲回。
10. 盈亏调整单已落盘，当前已明确它是库存调整单，不是资金单：`3601` 走成品库存，`3602` 走原料库存与库存日志。
11. 损耗单已落盘，当前已明确它和盈亏调整单同属库存链，但只接受正向损耗：`3801` 扣成品库存，`3802` 扣原料库存并记日志。
12. 客户来料单已落盘，当前已明确它不是单纯入库单，而是“原料库存 + 材质钱包 + 现金钱包 + 结价冲回”的复合链。
13. 供应商来料单已落盘，当前已明确它与客户来料共表共服务，但钱包主账口径不同：采购入库按现金/材质二选一，提纯入库可能两边都动。
14. 成品入库单已落盘，当前已明确它不是纯库存单，而是“成品库存 + 供应商钱包”双链模块；`0402/0403` 还会附带半成品回流与委外挂接。
15. 客户出料单和供应商出料单已落盘，当前已明确它们同属“原料库存扣减 + 钱包扣减”链，只是不同业务类型把钱包动作切成现金、材质或两者同时变化。
16. 客户退货单已落盘，当前已明确它不是单纯退回库存，而是“库存回库 + 客户钱包回写 + 结价冲回 + 维修仓位联动”的复合链。
17. 成品退厂单已落盘，当前已明确它不是纯出库单，而是“成品库存扣减 + 供应商钱包扣减 + 退厂来源挂接回写”的复合链；`0602` 还会继续影响半成品领料台账。
18. 成品退厂登记单已落盘，当前已明确它本身不动库存和钱包，而是承接“可退厂池、维修占用、分厂拆单、退厂执行占用”的前置模块。
19. 成品转料单已落盘，当前已明确它不是钱包链，而是“成品库存出库 + 原料库存入库 + 单件转料状态流”三链组合。
20. 当前最应该做的是继续向单件主链扩展，而不是重复整理已完成主链。
21. 单件入库单已落盘，当前已明确它不是简单建档单，而是“建单件 + 单件库存入 + 成品库存出 + 需求单累计 + 维修单回写”的复合执行链。
22. 单件调整单已落盘，当前已明确它不是删旧建新，而是“改单件本体 + 单件库存旧出新入 + 成品库存差额补扣 + 维修新件信息回写”的联动修改链。
23. 单件作废单已落盘，当前已明确它不是删单件，而是“改单件状态为 `DISCARD` + 单件库存出库 + 成品库存回收”，反审再整条回滚。
24. 单件盘点单操作已落盘，当前已明确它本质是“盘点基线快照 + 批次采集 + 差异查询”模块，审核/反审当前只改单头状态。
25. 库位调整单已落盘，当前已明确它是“锁单件 -> 审核改库位 + moveStock -> 解锁/反审回滚”的执行链。
26. 单件明细已落盘，当前已明确它不是审批单，而是 `wh_item + wh_item_log` 这组单件主档/事件表的查询与运维入口。
27. 盘点称重单已落盘，当前已明确它不是库存执行单，而是“称重录入 + 系统补齐基线 + 审核计算盈亏差值”的盘点分析单。
28. 维修单已落盘，当前已明确它不是简单登记单，而是维修域宿主链：审核/反审会联动单件挂接、客户退货累计、钱包结算/结价冲回，以及部分分支下的库存动作。
29. 维修入库单已落盘，当前已明确它不是普通收货单，而是“维修出库回收入库 + 供应商钱包入账 + 维修单累计回写 + 单件返仓/库存恢复”的复合执行链。
30. 维修出库单已落盘，当前已明确它不是普通出库单，而是“在库货转维修态出库 + 供应商钱包扣减 + 维修单上一轮入库结果清空 + 退厂登记池推进”的复合执行链。
31. 维修单详情已落盘，当前已明确它不是独立审批单，而是维修域明细宿主模块；终结时会同时推进维修明细、单件维修态和维修单头聚合完成态。
32. 维修单报表已落盘，当前已明确真实入口是 `maintain-bill-bi`；它不是单独 report 模块，而是把维修出库链和维修入库链按供应商、客户、部门、业务员、仓库和明细生命周期做双侧对照统计。

## 5. 当前未关闭事项

| 标记 | 来源模块 | 来源方法 | 摘要 | 当前状态 |
|------|---------|---------|------|----------|
| ⚠️ | 客留存 | `cancel` | “重新上柜”文案对应状态实际更新为 `RECYCLED` | 待确认 |
| ⚠️ | 客留存 | `migrateRetain` | 通过修改 `loginUser.wareIds` 放宽仓库权限 | 待确认 |
| ⚠️ | 客户订单 | `customerOrderStatusChange(Collection<Long>)` | 批量重算遇到不可处理订单会直接 `return` | 待确认 |
| ⚠️ | 出库结算单 | `deliveryRequest` | “发货申请”直接改成 `DELIVERY_ALLOW` | 待确认 |
| ⚠️ | 出库结算单 | `staffNoDelivered` | 同一次状态流里存在重复更新 | 待确认 |
| ⚠️ | 订单核销 | `OrderWriteoffApiImpl.updateProcessResult` | 已暴露 `oa_orderWriteoff` 流程 key，但审批回调实现被注释 | 待确认 |
| ⚠️ | 收款单 | `deleteReceivableBill` | 仅物理删除主表，未看到状态校验，也未同步清理明细和资金侧痕迹 | 待确认 |
| ⚠️ | 收款单 | `discard` | 只禁止 `CONFIRM`，未禁止 `DIVIDE`，已分账单据可能被直接作废而不回滚钱包分配 | 待确认 |
| ⚠️ | 付款单 | `deletePayableBill` | 仅物理删除主表，未看到状态校验，也未同步删除明细与资金侧痕迹 | 待确认 |
| ⚠️ | 付款单 | `confirm` | 只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 |
| ⚠️ | 客户应收账调整 | `deleteCustomerAdjustBill` | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 |
| ⚠️ | 客户应收账调整 | `confirm` | 只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 |
| ⚠️ | 供应商应付账调整 | `deleteSupplierAdjustBill` | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 |
| ⚠️ | 供应商应付账调整 | `confirm` | 只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 |
| ⚠️ | 供应商应付账调整 | `unConfirm` | 反审分支仍抛客户侧错误码，异常语义串模块 | 待确认 |
| ⚠️ | 调账单 | `deleteAccountAdjustBill` | 只删主表，没看到级联删除明细，也没见状态限制 | 待确认 |
| ⚠️ | 调账单 | `confirm` | 只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 |
| ⚠️ | 调账单 | `confirm/unConfirm/discard` | 单据不存在时仍抛客户应收调整侧错误码，异常语义串模块 | 待确认 |
| ⚠️ | 盈亏调整单 | `deleteProfitLossAdjustBill` | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 |
| ⚠️ | 盈亏调整单 | `createProfitLossAdjustBill/updateProfitLossAdjustBill` | `validDetails()` 已写好但被注释，当前未强制拦截空值或 0 值异常明细 | 待确认 |
| ⚠️ | 盈亏调整单 | `updateProfitLossAdjustBill` | 重算 `totalGoldWeight` 时未过滤 `null`，更新请求若出现空金重存在空指针风险 | 待确认 |
| ⚠️ | 损耗单 | `deleteWastageBill` | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 |
| ⚠️ | 损耗单 | `doGoodsStock` | 饰品损耗传入 `stock = 0`，只按金重扣库存；需确认业务上是否明确“不扣件数只扣重量” | 待确认 |
| ⚠️ | 损耗单 | `setTotalHeadData` | 熔金损耗单头 `totalWastageWeight` 取“熔前合计 - 熔后合计”，不是明细 `wastageWeight` 求和 | 待确认 |
| ⚠️ | 客户来料单 | `getCloseWalletData` | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定 `settleNo` | 待确认 |
| ⚠️ | 客户来料单 | `doCreateMaterialInboundBill` | `totalStockWeight` 建单时直接取负，报表若按“本次来料重”理解容易看反 | 待确认 |
| ⚠️ | 供应商来料单 | `InboundSupplierBuy#doBizType` | 采购入库用“现金是否大于 0”决定只走现金还是只走材质钱包，隐式规则较强 | 待确认 |
| ⚠️ | 成品入库单 | `deleteGoodsInboundBill` | 删除只看到了主表和明细，未见半成品记录、附件、配件的级联清理，也未见状态限制 | 待确认 |
| ⚠️ | 成品入库单 | `convertInbound` | “根据委外明细转成品入库明细”方法目前直接返回空列表，整段真实实现被注释 | 待确认 |
| ⚠️ | 成品入库单 | `confirm/infirm` | 库存更新调用里统一传 `G_PURCHASE_IN_STORAGE`，即使当前单据可能是 `0402/0403` | 待确认 |
| ⚠️ | 客户出料单 | `confirm` | 审核校验仍通过 `billName` 文案分支判断客户/供应商，不是稳定的 `billType/bizType` | 待确认 |
| ⚠️ | 供应商出料单 | `doCreateMaterialOutboundBill` | `2301/2302/2303` 的金额公式全部硬编码在同一方法里，后续改造容易串业务口径 | 待确认 |
| ⚠️ | 客户退货单 | `handlerCloseWallet` | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定 `settleNo` | 待确认 |
| ⚠️ | 客户退货单 | `deleteReturnBill` | 删除逻辑未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 |
| ⚠️ | 成品退厂单 | `delete(Long id)` | 删除接口当前直接返回 `false`，没有真实实现 | 待确认 |
| ⚠️ | 成品退厂单 | `doOutOfWareRejectRecord` | 反审时把登记池状态回写成 `OUTING`，不是 `NOT_OUT` | 待确认 |
| ⚠️ | 成品退厂登记单 | `delete(Long id)` | 直接物理删主表和明细，未见状态限制，也未释放维修占用 | 待确认 |
| ⚠️ | 成品退厂登记单 | `confirm(Long id)` | 只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 |
| ⚠️ | 成品转料单 | `deleteGoodsRecycleBill` | 删除直接物理删主表和明细，未见状态限制 | 待确认 |
| ⚠️ | 成品转料单 | `discard` | 作废只改单头状态，不回滚库存；必须依赖状态机保证未审核单据才可作废 | 待确认 |
| ⚠️ | 成品转料单 | `unConfirm` | 成品分支回滚原料库存时当前传的是 `details` 而不是 `goodsDetails` | 待确认 |
| ⚠️ | 单件入库单 | `deleteEntryBill` | 删除直接物理删主表和明细，未见状态限制 | 待确认 |
| ⚠️ | 单件入库单 | `confirm` | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态再次审核 | 待确认 |
| ⚠️ | 单件入库单 | `confirm/infirm` | 成品库存更新统一传 `SG_PURCHASE_IN_STORAGE`，即使真实单据可能是 `0801/0803` | 待确认 |
| ⚠️ | 单件入库单 | `infirm` | 反审直接物理删除 `wh_item`，不是状态回滚 | 待确认 |
| ⚠️ | 单件调整单 | `deleteAdjustBill` | 删除直接物理删主表和明细，未见状态限制 | 待确认 |
| ⚠️ | 单件调整单 | `discard` | 单据不存在时抛的是 `TRANSFER_BILL_NOT_EXISTS`，错误码串到调拨单模块 | 待确认 |
| ⚠️ | 单件调整单 | `updateProcessResult` | 未通过审批时也会先执行 `dealMaintain()` 与 `unlockByEpcList()` | 待确认 |
| ⚠️ | 单件调整单 | `updateGoodsStockAdjust` | 成品库存差额只看重量差，`num` 固定为 `0` | 待确认 |
| ⚠️ | 单件作废单 | `deleteCancelBill` | 删除直接物理删主表和明细，未见状态限制 | 待确认 |
| ⚠️ | 单件作废单 | `confirm` | 方法名叫“审核”，真实语义却是发起审批流程并把单据推到 `CONFIRMING` | 待确认 |
| ⚠️ | 单件作废单 | `infirm` | 跨日权限错误文案写成“产品包跨日审核反审权限” | 待确认 |
| ⚠️ | 单件作废单 | `infirm` | 反审状态校验失败时提示“必须是核销状态反审核”，错误文案串到订单核销模块 | 待确认 |
| ⚠️ | 单件盘点单操作 | `approved` | 审核当前只改单头状态，没看到依据差异正式回写 `wh_item`、库存或日志 | 待确认 |
| ⚠️ | 单件盘点单操作 | `returnApproved` | 反审只允许撤当天审核单据，且没有跨日权限配置分支 | 待确认 |
| ⚠️ | 单件盘点单操作 | `createInventoryBill` | 24 小时排他校验用 `bill_status != '4'`，对状态码含义存在硬编码依赖 | 待确认 |
| ⚠️ | 单件盘点单操作 | `deleteItemInventoryBill` | 通用 CRUD 删除只删主表，未见批次、明细、库存备份级联清理 | 待确认 |
| ⚠️ | 库位调整单 | `deleteShelvesBill` | 删除只删主表，未见明细级联、EPC 解锁和状态限制 | 待确认 |
| ⚠️ | 库位调整单 | `updateShelvesBill` | 更新时先算出 `shelves.setTotal...`，但真正 `updateById` 用的是 `updateObj`，汇总字段可能没写回 | 待确认 |
| ⚠️ | 库位调整单 | `discard` | 作废分支里 `itemMap` 以 `itemId` 为 key，却按 `d.getEpc()` 取值，存在取空风险 | 待确认 |
| ⚠️ | 库位调整单 | `ShelvesBillDetailMapper.selectList` | 导出查询最后直接 `return selectList()`，前面构造的过滤条件没有真正使用 | 待确认 |
| ⚠️ | 单件明细 | `unlockByEpcList` | 批量解锁不校验当前业务状态，可能把别的链路锁住的单件直接解开 | 待确认 |
| ⚠️ | 单件明细 | `ItemMapper.selectList(ItemExportReqVO)` | 导出口径里 `partsFee` 过滤写成了 `getPartsPrice`，字段映射看起来不一致 | 待确认 |
| ⚠️ | 单件明细 | `getItemByItemNo` | 方法名按条码查，但实现是 `epc = itemNo OR item_no = itemNo`，语义比名字更宽 | 待确认 |
| ⚠️ | 单件明细 | `releaseCustomerLock` | 同一流程里既 `createItemLog(...)` 又手工 `save(itemLog)`，客锁释放日志可能重复 | 待确认 |
| ⚠️ | 盘点称重单 | `confirm` | 审核当前只计算并保存盈亏差值，没看到正式回写 `wh_sku_stock` 或 `wh_material_stock` | 待确认 |
| ⚠️ | 盘点称重单 | `deleteInventoryWeighingBill` | 删除主表和明细时没看到状态限制 | 待确认 |
| ⚠️ | 盘点称重单 | `fillData/handleDetailList` | 详情页会过滤 `isGen = 1` 明细，但汇总又依赖全量明细，前后端容易对不上口径 | 待确认 |
| ⚠️ | 盘点称重单 | `doProfitLossAccessory` | 只有成品称重审核时才初始化 `inventoryWeighingBillStock` 账面备份，原料称重未见对应备份表动作 | 待确认 |
| ⚠️ | 维修单 | `deleteMaintainBill` | 删除当前只删主表，未见明细级联、状态限制和来源挂接清理 | 待确认 |
| ⚠️ | 维修单 | `confirm / infirm` | 单头审核态只改 `billStatus`，真正业务动作分散在 `MaintainBillConfirmBusiness`，维护时很容易只改一半链路 | 待确认 |
| ⚠️ | 维修单 | `setToCounter` | 当前只拦 `replyStatus = 4`，未见对未审核、已作废等异常状态的显式限制 | 待确认 |
| ⚠️ | 维修单 | `updateTotalColumn` | 单头 `replyStatus` 完全依赖累计量聚合，若下游维修入库/结算/上柜回写漏一处，维修单进度会失真 | 待确认 |
| ⚠️ | 维修入库单 | `updateRelateOutBill` | 草稿保存阶段就会把维修出库明细挂上 `pushMaintainInBillDetailId`，不是审核后才挂接 | 待确认 |
| ⚠️ | 维修入库单 | `itemHandle` | 单件审核时把 `lockStatus` 直接改成未锁，反审时改成锁定；需要确认是否总能代表维修链真实锁语义 | 待确认 |
| ⚠️ | 维修入库单 | `billHandle` | 反审时单头改成 `2`，但明细 `billStatus` 仍被写成 `1`，头明细状态看起来不一致 | 待确认 |
| ⚠️ | 维修入库单 | `preCheck` | 反审禁止条件依赖 `totalPushCount` 与“审核后是否又生成维修出库单”，后续若有别的下推链路要一起补校验 | 待确认 |
| ⚠️ | 维修出库单 | `updateRelateData` | 草稿保存阶段就会把退厂登记单头改成 `OUTING`，不是审核后才推进 | 待确认 |
| ⚠️ | 维修出库单 | `maintainOutNumHandle` | 审核时会把维修单明细上一轮入库结果整组清空，维护时很容易误伤 `newItem.../last...` 口径 | 待确认 |
| ⚠️ | 维修出库单 | `preCheck` | 反审强依赖 `totalInNum = 0` 与 `totalPushCount = 0`，只要后续已有维修入库或下推链就彻底锁死 | 待确认 |
| ⚠️ | 维修出库单 | `settleHandle` | 钱包口径是“材质重量 + 工费金额”一起扣减，若后续只改其中一边会造成供应商账不平 | 待确认 |
| ⚠️ | 维修单详情 | `finalization` | 终结只拦“已完成”明细，没看到对真实流转完成度的前置校验，理论上可把未闭环明细直接终结 | 待确认 |
| ⚠️ | 维修单详情 | `finalization` | 终结时把 `outNum/inNum` 直接补成至少等于 `num`，可能掩盖真实未闭环数量 | 待确认 |
| ⚠️ | 维修单详情 | `updateStepLocal` | 以仓库名称 `"编货仓"` 文案判断并把 `replyStatus` 写成 `"6"`，属于脆弱文本耦合 | 待确认 |
| ⚠️ | 维修单详情 | `createItemEntryDetailVOByIds/createItemAdjustDetailVOByIds` | 下推单件入库和调整都依赖“最近一次维修入库明细”作为当前结果基础，若最近一笔缺失或滞后，会把旧结果继续下传 | 待确认 |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalCustomer` | 控制器里把客户报表业务类型直接写死成“客户维修 + 展厅维修”，若后续新增客户侧维修类型，报表会天然漏数 | 待确认 |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalDetail_from_where` | `replyType=3` 用 `step_ware_id = 457` 判断“在厂”，属于硬编码仓库 ID 口径 | 待确认 |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalDetailFillDataExpand` | 报表展示的“已回/未回/终结”是业务重算结果，不完全等于明细表 `replyStatus` 原值 | 待确认 |
| ⚠️ | 维修单报表 | `maintainbillsettle` | 结算明细模块名字容易被误判成“维修报表”，但它实际上只是 `wh_maintain_bill_settle` 的 CRUD/导出 | 待确认 |

## 6. 本轮关键结论

1. `wh_customer_order_item.local_status` 是订单主状态重算的核心桥接字段。
2. `wh_customer_retain.status`、`wh_pack.pack_status`、`wh_pack.transfer_status`、`wh_sale_bill.bill_status`、`wh_delivery_bill.status` 已分别沉淀成状态机图。
3. 产品包的主状态和调拨状态必须分开分析，不能混用。
4. `ship/reship` 不等于真正发货成功，真正成功发生在异步回调。
5. 成品调拨单在部分场景下不仅回写产品包，还会回写结算单调拨字段。
6. 订单核销的 `writeoffNum/writeoffGramWeight` 实际等于“合格核销量”，需求单侧累计值吃的是这组值而不是总回货量。
7. 委外单的“待核销/部分核销/核销完毕”状态是在订单核销审核/反审时按全量委外明细重新聚合得出。
8. 收款单审核/反审会同时影响银行卡余额和钱包余额两条资金链，分账/取消分账只在钱包侧重分配归属。
9. 付款单审核/反审同样是双资金链动作，但方向与收款单相反：审核扣银行卡、加钱包。
10. 客户应收账调整审核/反审只影响钱包，但一条明细可同时改金额账和金重账两种钱包。
11. 供应商应付账调整与客户应收账调整是镜像链路：同样只动钱包，不动银行卡，且审核/反审分别用 `DEPOSIT/DEBT` 口径写现金钱包与材质钱包。
12. 调账单是“调出账户/调入账户”双分录钱包链：金额和金重都会分别生成一对方向相反的钱包动作。
13. 盈亏调整单是库存差异落账链，不碰钱包：饰品调整走成品库存，原料调整走原料库存与库存日志。
14. 损耗单也是库存链，但口径比盈亏调整更严格：只允许正损耗，审核扣库存，反审再加回。
15. 客户来料单是复合链：审核/反审同时影响原料库存、客户材质钱包、客户现金钱包，且 `CLOSING_PRICE(02)` 会额外做结价冲回。
16. 供应商来料单与客户来料共表共服务，但供应商钱包主账是按明细汇总后经工厂分支过滤，不是按结算明细主导。
17. 成品入库单审核/反审会同时改成品库存和供应商钱包；转成品入库还会联动半成品回流累计、供应商领料台账和委外挂接。
18. 客户出料单与供应商出料单审核/反审都会同时改原料库存和钱包；差别不在库存方向，而在不同业务类型对现金钱包/材质钱包的过滤规则。
19. 客户退货单审核/反审会同时改单件/成品库存和客户钱包，且 `CLOSING_PRICE(02)` 在退货链里仍然是一笔独立的反向冲回动作。
20. 成品退厂单审核/反审会同时改单成品库存和供应商钱包，并把退厂登记池或半成品调拨池的占用状态一并推进或回滚。
21. 成品退厂登记单自身不触发库存/钱包过账；它的核心字段是 `billStatus + outOfWare` 双状态，以及维修明细 `recordNum/recordId` 占用关系。
22. 成品转料单审核/反审会同时改原料库存和成品库存；如果明细带 `itemNo`，还会继续下钻单件转料状态流。
23. 单件入库单审核/反审会同时改 `wh_item`、单件库存、成品库存、需求单累计和维修单累计，是单件主链的真正起点。
24. 单件调整单审批通过后会直接覆写原单件，并生成“旧件出库日志 + 新件入库日志”双日志，同时按金重差额回写成品库存。
25. 单件作废单审核时改单件状态为 `DISCARD`，并做“单件库存出库 + 成品库存回收”；反审再整条回滚。
26. 单件盘点单操作建单时会先冻结 `wh_item` 与库存汇总快照，后续批次只是在快照池里标记“盘到/盘盈”，审核本身当前不做正式库存过账。
27. 库位调整单审核/反审会同时影响 `wh_item.localId`、库位库存分布和单件锁状态，属于单件库存运维链里的执行单。
28. 单件明细模块是单件域的统一主档入口：查询、导出、财务视图、复核、客锁释放都建立在 `wh_item + wh_item_log` 之上。
29. 盘点称重单审核时真正回写的是“盈亏差值结果”，不是直接改库存；系统生成明细 `isGen = 1` 是它的关键基线机制。
30. 维修单不是单一审核单，而是维修域宿主链；真正业务动作沉在 `MaintainBillConfirmBusiness`，单头 `replyStatus` 还要依赖后续维修入库、结算、上柜等累计值聚合。
31. 维修入库单是维修域里第一张真正把“返厂货回来”落到库存和供应商钱包上的执行单；它同时还负责把维修单和维修出库单两边累计数闭环。
32. 维修出库单是维修域里把“在库货送厂”的真正执行单；它会先清空维修单明细上一轮入库结果，再让后续维修入库重新写回新一轮结果。

## 7. 下一优先任务

1. 委外加工订单
2. 基础主数据或维修域残余查询模块
3. 维修结算明细或维修域剩余辅助模块

原因：
1. 维修执行链和查询链已经通过维修报表对上了，下一步最顺的是直接切委外主线。
2. 委外加工订单仍然和订单核销、成品入库、转成品入库等链路强耦合，继续往下做能把关键交易主线补齐。
3. 委外主线收口后，再回到维修结算明细或基础主数据，切换成本最低。

## 8. 下一会话启动顺序

1. [analysis-workflow.md](/D:/ws/code/wms-api/docs/business/analysis-workflow.md)
2. [session-handoff.md](/D:/ws/code/wms-api/docs/business/session-handoff.md)
3. [overview.md](/D:/ws/code/wms-api/docs/business/overview.md)
4. 下一目标模块文档或源码
5. 相关状态机图与实体图
6. 相关总表：
   - [cross-module-writeback.md](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)
   - [external-interactions.md](/D:/ws/code/wms-api/docs/business/external-interactions.md)
   - [field-semantics.md](/D:/ws/code/wms-api/docs/business/field-semantics.md)
   - [change-impact.md](/D:/ws/code/wms-api/docs/business/change-impact.md)

## 委外加工订单续补（2026-03-31）
- 已完成模块：委外加工订单
- 新增文档：[outsourcingorder.md](/D:/ws/code/wms-api/docs/business/outsourcingorder.md)
- 新增图： [entity-outsourcing-order-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-outsourcing-order-chain.md)、[outsourcing-order-state.md](/D:/ws/code/wms-api/docs/business/model/outsourcing-order-state.md)
- 当前结论：委外加工订单是“需求单委外占用链 + 订单核销状态聚合宿主 + 单件/成品入库来源池”。
- 当前结论：建单/改单/作废/终止核销都会回写需求单，审核只把单据推到待核销并决定 `receiveStatus`，真正的“部分核销/核销完毕”由订单核销链重算。
- 当前结论：BPM 回调当前只落 `processResult/approvedMan/approvedTime`，没有直接驱动业务状态。
- 新增待确认：`calcSumGramWeight(...)` 用 `demandNum` 计算 `sumGramWeight`。
- 新增待确认：`discard(...)` 未见对 `CANCELLATION/DELIVERED/FINISH` 的统一状态限制。
- 新增待确认：`selectWaitEntryPage` 的供应商过滤别名写成 `b.supplier_id`。
- 下一优先：基础主数据或维修域残余查询模块；维修结算明细；委外相关辅助查询模块。

## 维修结算明细续补（2026-03-31）
- 已完成模块：维修结算明细
- 新增文档：[maintainbillsettle.md](/D:/ws/code/wms-api/docs/business/maintainbillsettle.md)
- 新增图： [entity-maintain-settle-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-maintain-settle-chain.md)、[maintain-bill-settle-state.md](/D:/ws/code/wms-api/docs/business/model/maintain-bill-settle-state.md)
- 当前结论：维修结算明细是维修单财务结算子表，本身不驱动状态，但会被维修确认链直接拿来构造客户材质钱包、现金钱包和结价冲回钱包。
- 当前结论：`MaintainBillStoreVO` 已带 `settleList`，但当前维修单主服务没看到明显的保存、删除、详情回显接线。
- 新增待确认：维修单建单/改单是否在别处落 `wh_maintain_bill_settle`。
- 新增待确认：详情接口为什么没有把 `settleList` 回显出来。
- 下一优先：基础主数据或维修域残余查询模块；委外相关辅助查询模块。

## 委外加工订单明细续补（2026-03-31）
- 已完成模块：委外加工订单明细
- 新增文档：[outsourcingorderdetail.md](/D:/ws/code/wms-api/docs/business/outsourcingorderdetail.md)
- 新增图： [entity-outsourcing-detail-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-outsourcing-detail-chain.md)、[outsourcing-order-detail-state.md](/D:/ws/code/wms-api/docs/business/model/outsourcing-order-detail-state.md)
- 当前结论：委外明细模块承担了来货轨迹查询、单件入库待入库池、转成品入库来源池、工厂导出和采购汇总几条辅助链。
- 当前结论：它依赖委外明细累计字段、核销明细和单件入库明细共同形成分析口径，不是纯 CRUD 子表。
- 新增待确认：`getWaitEntryDetail` 为何按 `outsourcing_order_no` 而不是明细 ID 聚合已入库量。
- 新增待确认：采购汇总和委外主表 `sumGramWeight` 的重量口径不一致。
- 下一优先：基础主数据或委外残余辅助查询模块。

## 结算方式续补（2026-03-31）
- 已完成模块：结算方式
- 新增文档：[settlement.md](/D:/ws/code/wms-api/docs/business/settlement.md)
- 新增图： [entity-settlement-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-settlement-chain.md)、[settlement-state.md](/D:/ws/code/wms-api/docs/business/model/settlement-state.md)
- 当前结论：结算方式是跨销售、退货、来料、维修的主数据字典，不是业务执行单。
- 当前结论：`RECIRCULATION/CLOSING_PRICE` 已被多个业务链硬依赖，主数据变更影响面很大。
- 下一优先：基础主数据模块、委外或维修残余辅助查询模块。

## 仓库结构续补（2026-03-31）
- 已完成模块：仓库档案、库区档案、库位档案
- 新增文档：[warehouse.md](/D:/ws/code/wms-api/docs/business/warehouse.md)、[warehousearea.md](/D:/ws/code/wms-api/docs/business/warehousearea.md)、[warehouselocation.md](/D:/ws/code/wms-api/docs/business/warehouselocation.md)
- 新增图： [warehouse-structure-state.md](/D:/ws/code/wms-api/docs/business/model/warehouse-structure-state.md)、[entity-warehouse-structure-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-warehouse-structure-chain.md)
- 当前结论：仓库结构主数据带自动建档规则，建仓自动建 `A` 库区，建库区自动建 `A0` 库位。
- 当前结论：库位还带默认库位兜底与共享库位开关，是很多业务单据默认仓位来源。
- 新增待确认：`A0/A01` 默认库位口径不一致。
- 新增待确认：`getDefaultLocal(...)` 插入默认库位后为什么返回 `null`。
- 下一优先：客户/供应商/材质/大类等基础主数据模块。

## 客户档案续补（2026-03-31）
- 已完成模块：客户档案
- 新增文档：[customer.md](/D:/ws/code/wms-api/docs/business/customer.md)
- 新增图： [entity-customer-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-customer-domain-chain.md)、[customer-entity-state.md](/D:/ws/code/wms-api/docs/business/model/customer-entity-state.md)
- 当前结论：客户档案是“客户主档 + 财务账户 + BPM审批缓存 + 商城账号 + 权限过滤 + 变更日志”复合主数据模块。
- 当前结论：顶级客户审批通过后才真正启用；子客户走继承父客户属性的免审批建档链。
- 当前结论：客户名称变更会继续回写需求单、账户名和钱包名，不能把它当成静态资料表。
- 新增待确认：删除为什么只拦子客户和钱包，不拦历史业务引用。
- 新增待确认：批量提审是否也应限制为顶级客户。
- 下一优先：供应商档案、材质/大类等高频基础主数据模块。

## 供应商档案续补（2026-03-31）
- 已完成模块：供应商档案
- 新增文档：[supplier.md](/D:/ws/code/wms-api/docs/business/supplier.md)
- 新增图： [entity-supplier-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-supplier-domain-chain.md)、[supplier-entity-state.md](/D:/ws/code/wms-api/docs/business/model/supplier-entity-state.md)
- 当前结论：供应商档案是“主档 + 财务账户 + 开放钱包删除保护 + 导入覆盖更新”的轻量主数据宿主链。
- 当前结论：它没有客户档案那种 BPM 生效链，但账户绑定从建档开始就是强约束。
- 新增待确认：删除为什么不处理账户，只删主档。
- 新增待确认：停用供应商是否需要同步停用账户/钱包。
- 下一优先：材质、品类、大类等库存/钱包/结算高频引用主数据。

## 材质档案续补（2026-03-31）
- 已完成模块：材质档案
- 新增文档：[texture.md](/D:/ws/code/wms-api/docs/business/texture.md)
- 新增图： [entity-texture-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-texture-domain-chain.md)、[texture-entity-state.md](/D:/ws/code/wms-api/docs/business/model/texture-entity-state.md)
- 当前结论：材质档案是库存、钱包、SKU、半成品分类共用的高频口径主数据。
- 当前结论：材质改名/改编码不会停留在主表，会继续同步 `wh_sku` 冗余字段。
- 新增待确认：材质删除为什么没有引用保护。
- 新增待确认：材质编号是否真的允许负数/小数。
- 下一优先：大类/品类主数据，继续收库存与商品归类口径。

## 大类档案续补（2026-03-31）
- 已完成模块：大类档案
- 新增文档：[bigclass.md](/D:/ws/code/wms-api/docs/business/bigclass.md)
- 新增图： [entity-bigclass-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-bigclass-domain-chain.md)、[bigclass-entity-state.md](/D:/ws/code/wms-api/docs/business/model/bigclass-entity-state.md)
- 当前结论：大类档案是“商品归类 + 计价规则 + SKU冗余回写”的规则型主数据。
- 当前结论：大类变更会直接影响 SKU 冗余字段和多个商品归类口径。
- 新增待确认：大类删除为什么没有引用保护。
- 新增待确认：业务上是否允许重名大类。
- 下一优先：品类/子品类主数据，继续把商品结构层补齐。

## 品类结构续补（2026-03-31）
- 已完成模块：品类档案、子品类
- 新增文档：[category.md](/D:/ws/code/wms-api/docs/business/category.md)、[categorysub.md](/D:/ws/code/wms-api/docs/business/categorysub.md)
- 新增图： [entity-category-structure-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-category-structure-chain.md)、[category-structure-state.md](/D:/ws/code/wms-api/docs/business/model/category-structure-state.md)
- 当前结论：品类结构层同时联动 WMS 内部商品结构和商城分类树，不能只当成本地字典。
- 当前结论：一级品类和子品类变更都会继续回写 SKU 冗余字段。
- 新增待确认：删品类/删子品类为什么没有对子结构、SKU 和商城分类树做处理。
- 新增待确认：子品类启用列表接口为何未显式按 `status` 过滤。
- 下一优先：更细一层的品类结构模块或商品档案主链。

## 产品资料续补（2026-03-31）
- 已完成模块：产品资料
- 新增文档：[product.md](/D:/ws/code/wms-api/docs/business/product.md)
- 新增图： [entity-product-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-product-domain-chain.md)、[product-entity-state.md](/D:/ws/code/wms-api/docs/business/model/product-entity-state.md)
- 当前结论：产品资料是商品主档来源池，直接挂接品类、材质和产品类型字典。
- 当前结论：导入链不是简单主表导入，还包含品类编号解析与字典值转换。
- 新增待确认：删产品为什么没有引用保护。
- 新增待确认：建档为什么不显式校验品类/材质是否存在。
- 下一优先：SKU 主链或更细商品结构模块，继续往可执行商品对象收口。

## 款式库续补（2026-03-31）
- 已完成模块：款式库
- 新增文档：[sku.md](/D:/ws/code/wms-api/docs/business/sku.md)
- 新增图： [entity-sku-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-domain-chain.md)、[sku-entity-state.md](/D:/ws/code/wms-api/docs/business/model/sku-entity-state.md)
- 当前结论：SKU 是商品执行对象宿主链，上承商品结构与审批，下接默认供应商 BOM、库存、单件、搜索和导出权限。
- 当前结论：建款时默认状态是 `DISABLE`，审批通过后缓存版才真正覆盖主表。
- 新增待确认：删款式为什么没有对库存、单件、BOM 和历史业务引用做保护。
- 新增待确认：注释掉的商城同步/标签同步是否已经迁移到别处。
- 下一优先：库存主链或供应商BOM细分模块，继续把 SKU 周边执行对象补齐。

## 供应商款式 / BOM 续补（2026-03-31）
- 已完成模块：供应商款式 / BOM
- 新增文档：[suppliersku.md](/D:/ws/code/wms-api/docs/business/suppliersku.md)
- 新增图： [entity-supplier-sku-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-supplier-sku-chain.md)、[supplier-sku-bom-state.md](/D:/ws/code/wms-api/docs/business/model/supplier-sku-bom-state.md)
- 当前结论：这层不是独立审批模块，而是 `sku` 的供应商 BOM 与默认费用来源子图。
- 当前结论：默认供应商 `isDirect` 行会把费用投影回 `wh_sku`，因此改 BOM 不只是改明细展示。
- 当前结论：accessories / part 都是卫星表，SKU 详情、导出和采购查询看到的是聚合 BOM 视图。
- 新增待确认：`getBomList(...)` 把供应商查询写死成 `supplierCode = 00`，是否本来就是商品部专用口径。
- 新增待确认：`deleteSkuSpecsBySkuNos(...)` 命名为什么会和真实删除对象不一致。
- 下一优先：SKU 规格层或更细商品结构卫星模块，继续把款式执行对象补齐。

## SKU 规格续补（2026-03-31）
- 已完成模块：SKU 规格
- 新增文档：[skuspecs.md](/D:/ws/code/wms-api/docs/business/skuspecs.md)
- 新增图： [entity-sku-specs-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-specs-chain.md)、[sku-specs-state.md](/D:/ws/code/wms-api/docs/business/model/sku-specs-state.md)
- 当前结论：规格层是 `sku` 的卫星层，不是独立审批模块。
- 当前结论：规格真实生命周期分成新建款缓存、改款缓存和正式生效三层，不只是 `wh_sku_specs` 单表。
- 新增待确认：`wh_new_sku_specs / wh_update_sku_specs` 到 `wh_sku_specs` 的正式搬运动作具体落在哪个 `sku` 主链节点。
- 新增待确认：规格层 `isDirect` 与供应商 BOM 层的同名字段是否应该拆语义。
- 下一优先：`newsku / updatesku` 主链，继续把 SKU 审批缓存对象补齐。

## SKU 审批域续补（2026-03-31）
- 已完成模块：新建款管理、款式修改单
- 新增文档：[newsku.md](/D:/ws/code/wms-api/docs/business/newsku.md)、[updatesku.md](/D:/ws/code/wms-api/docs/business/updatesku.md)
- 新增图： [entity-sku-approval-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-domain-chain.md)、[sku-approval-domain-state.md](/D:/ws/code/wms-api/docs/business/model/sku-approval-domain-state.md)
- 当前结论：SKU 审批域真实由 `newsku` 新建缓存链、`updatesku` 改款缓存链和正式 `sku` 生效链三层组成。
- 当前结论：新建款和改款都不是薄 BPM 包装，而是带企微待办、抄送、消息提醒、导入导出和批量修订能力的重业务宿主。
- 新增待确认：新建款和改款审批通过时，规格/BOM/辅石/配件四组缓存卫星表分别在哪个节点正式搬运到生效表。
- 新增待确认：改款链里的费用字段集合和阶段枚举是否已有统一测试或校验约束。
- 下一优先：新建款/改款日志与明细卫星层，继续把 SKU 审批域补齐。

## SKU 审批明细宿主续补（2026-03-31）
- 已完成模块：新建款明细、改款明细
- 新增文档：[newskudetail.md](/D:/ws/code/wms-api/docs/business/newskudetail.md)、[updateskudetail.md](/D:/ws/code/wms-api/docs/business/updateskudetail.md)
- 新增图： [entity-sku-approval-detail-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-detail-chain.md)、[sku-approval-detail-state.md](/D:/ws/code/wms-api/docs/business/model/sku-approval-detail-state.md)
- 当前结论：审批域列表、企微待办和批次视图的真实宿主是 detail 表，不是 log 表。
- 当前结论：改款 detail 还额外承担未同步池和按改款类型过滤的执行语义。
- 新增待确认：detail 宿主与正式 `sku` 主档之间是否已有字段同步一致性校验。
- 新增待确认：日志漏写时，已审/已驳回企微视图会不会直接缺批次历史。
- 下一优先：`newsku / updatesku` 的供应商/BOM 缓存卫星层，继续把审批域补齐。

## SKU 审批域 BOM 缓存续补（2026-03-31）
- 已完成模块：SKU 审批域供应商 BOM 缓存
- 新增文档：[skuapprovalsupplier.md](/D:/ws/code/wms-api/docs/business/skuapprovalsupplier.md)
- 新增图： [entity-sku-approval-bom-cache-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-bom-cache-chain.md)、[sku-approval-bom-cache-state.md](/D:/ws/code/wms-api/docs/business/model/sku-approval-bom-cache-state.md)
- 当前结论：审批期间的供应商头、辅石/辅料、半成品/配件都先停留在 BOM 缓存层，不直接写正式 `supplier-sku`。
- 当前结论：默认供应商 `isDirect` 在审批缓存期就已确定，后续正式生效大概率直接复用这个结果。
- 新增待确认：审批通过时 `bomSerialNo` 是否直接沿用缓存值搬到正式 BOM。
- 新增待确认：改款侧按 `skuNo` 查缓存的结果是否会跨多次改款混看。
- 下一优先：SKU 审批域剩余导入/批量修改/同步侧辅助链，继续把这组审批生态补齐。

## SKU 审批域辅助链续补（2026-03-31）
- 已完成模块：SKU 审批域辅助链
- 新增文档：[skuapprovalsupport.md](/D:/ws/code/wms-api/docs/business/skuapprovalsupport.md)
- 新增图： [entity-sku-approval-support-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-support-chain.md)、[sku-approval-support-state.md](/D:/ws/code/wms-api/docs/business/model/sku-approval-support-state.md)
- 当前结论：导入分析、批量修改、同步生效和阶段回退共同构成了 SKU 审批域的执行引擎。
- 当前结论：新建款同步是整套生效搬运；改款同步按普通修改、设计元素、下架、上架四类拆分。
- 新增待确认：新建款生成正式 `skuNo` 的规则和 SKU 规则服务是否已经完整统一。
- 新增待确认：改款四条同步分支是否覆盖了未来新增阶段类型。
- 下一优先：切出 SKU 审批域，继续收其他未覆盖业务或高频基础模块。

## SKU 规则主数据续补（2026-03-31）
- 已完成模块：设计元素、生产工艺
- 新增文档：[designelements.md](/D:/ws/code/wms-api/docs/business/designelements.md)、[productionprocess.md](/D:/ws/code/wms-api/docs/business/productionprocess.md)
- 新增图： [entity-sku-rule-master-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-rule-master-chain.md)、[sku-rule-master-state.md](/D:/ws/code/wms-api/docs/business/model/sku-rule-master-state.md)
- 当前结论：这两类规则主数据都是 SKU / newsku / updatesku 的高频校验与映射源，不能当普通资料表看。
- 当前结论：设计元素更像“品类 + 字典”混合驱动；生产工艺更像自动编码的规则选项主数据。
- 新增待确认：设计元素、生产工艺若被历史 SKU 引用，业务上是否允许改名或停用。
- 新增待确认：生产工艺两位编码是否存在容量上限风险。
- 下一优先：继续收 SKU 生态里剩余的规则主数据，如产线/系列/套系映射层。

## 系列与套系映射续补（2026-03-31）
- 已完成模块：系列与套系映射
- 新增文档：[skuseries.md](/D:/ws/code/wms-api/docs/business/skuseries.md)
- 新增图： [entity-sku-series-set-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-series-set-chain.md)、[sku-series-set-state.md](/D:/ws/code/wms-api/docs/business/model/sku-series-set-state.md)
- 当前结论：系列是轻量规则主档；系列映射是批量关系维护；套系映射已经进入主档回写和缓存失效层。
- 当前结论：套系修改会先拆旧挂接、再重挂新关联，不是普通更新单行关系。
- 新增待确认：单个 SKU 是否允许同时落在多个不同套系组合里。
- 新增待确认：系列是否也应该像套系一样回写到 `wh_sku` 冗余字段。
- 下一优先：继续收 SKU 周边剩余的规则映射或关系主数据模块。

## SKU 编码规则续补（2026-03-31）
- 已完成模块：SKU 编码规则
- 新增文档：[skurule.md](/D:/ws/code/wms-api/docs/business/skurule.md)
- 新增图： [entity-sku-rule-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-rule-chain.md)、[sku-rule-state.md](/D:/ws/code/wms-api/docs/business/model/sku-rule-state.md)
- 当前结论：这层是正式 `skuNo` 的序列规则宿主，不是普通主数据表。
- 当前结论：`skurulelog` 才是实际落号轨迹表，可以按 `skuNo` 反查编码落号历史。
- 新增待确认：是谁负责在正式落号后回写 `wh_sku_rule.serialNo` 并同步写规则日志。
- 新增待确认：4 位流水号在同一三元组下是否有容量风险。
- 下一优先：收 `copysetting` 这类影响 SKU 审批域企微/抄送可见性的配置模块。

## 抄送配置续补（2026-03-31）
- 已完成模块：抄送配置
- 新增文档：[copysetting.md](/D:/ws/code/wms-api/docs/business/copysetting.md)
- 新增图： [entity-copy-setting-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-copy-setting-chain.md)、[copy-setting-state.md](/D:/ws/code/wms-api/docs/business/model/copy-setting-state.md)
- 当前结论：这条配置链直接影响新建款/改款企微待办和已审/已驳回页面的抄送可见性与转办入口。
- 当前结论：当前真实生效的是抄送人和转办人，不是审批人配置。
- 新增待确认：同一菜单多条启用配置并存时，默认取最新一条是否符合预期。
- 新增待确认：`contains(loginUserId)` 的命中方式是否可能误判抄送人。
- 下一优先：继续收 SKU 周边剩余规则或关系模块。

## SKU 供应商库续补（2026-03-31）
- 已完成模块：SKU 供应商库
- 新增文档：[skusupplier.md](/D:/ws/code/wms-api/docs/business/skusupplier.md)
- 新增图： [entity-sku-supplier-index-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-supplier-index-chain.md)、[sku-supplier-index-state.md](/D:/ws/code/wms-api/docs/business/model/sku-supplier-index-state.md)
- 当前结论：这层是正式 SKU 与供应商的轻量归属索引层，不承载 BOM 费用结构。
- 当前结论：查询页会补图片和名称，所以它本身也是一个视图组装层。
- 新增待确认：正式 SKU 摘要字段更新后，`wh_sku_supplier` 的冗余工艺字段是否需要回刷。
- 新增待确认：是否需要限制同供应商下工厂款号重复等更细业务约束。
- 下一优先：继续收 SKU 周边剩余规则与配置模块。

## 客户侧 SKU 关系域续补（2026-03-31）
- 已完成模块：客户款式、客户款式库、老庙客户款式库
- 新增文档：[customersku.md](/D:/ws/code/wms-api/docs/business/customersku.md)、[skucustomer.md](/D:/ws/code/wms-api/docs/business/skucustomer.md)、[skucustomerlm.md](/D:/ws/code/wms-api/docs/business/skucustomerlm.md)
- 新增图： [entity-customer-sku-domain-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-customer-sku-domain-chain.md)、[customer-sku-domain-state.md](/D:/ws/code/wms-api/docs/business/model/customer-sku-domain-state.md)
- 当前结论：`customersku` 是现行客户款式主链，带客户工费、直采、权限筛选、导入和 BOM 视图补齐。
- 当前结论：`skucustomer` 是旧版轻量索引层，虽然已 `@Deprecated`，但外部 API 仍在直接调用。
- 当前结论：`skucustomerlm` 是老庙渠道专用外部商品投影缓存，字段体系明显不同于普通客户款。
- 新增待确认：三条链长期是否要收口到同一客户款式主域，还是继续并行维护。
- 新增待确认：正式 SKU 变更后，旧索引层和老庙映射层是否存在统一回刷机制。
- 下一优先：继续收 SKU 周边剩余关系/配置模块，优先看客户规格或客户定制相关残余链。

## 款式模号续补（2026-03-31）
- 已完成模块：款式模号
- 新增文档：[skumodel.md](/D:/ws/code/wms-api/docs/business/skumodel.md)
- 新增图： [entity-sku-model-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sku-model-chain.md)、[sku-model-state.md](/D:/ws/code/wms-api/docs/business/model/sku-model-state.md)
- 当前结论：这层是 SKU 重量段分桶后的模号缓存宿主，不是薄字典。
- 当前结论：老庙共享状态 `shareStatusLm` 直接挂在模号层。
- 新增待确认：重量段存在性判断疑似反向，是否是历史 bug 还是重量段服务口径特殊。
- 新增待确认：模号删除后，共享库存/老庙导出链是否允许断引用。
- 下一优先：继续收老庙共享库存链或模号下游使用模块。

## 共享库存续补（2026-03-31）
- 已完成模块：共享库存
- 新增文档：[sharestock.md](/D:/ws/code/wms-api/docs/business/sharestock.md)
- 新增图： [entity-share-stock-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-share-stock-chain.md)、[share-stock-state.md](/D:/ws/code/wms-api/docs/business/model/share-stock-state.md)
- 当前结论：这条链的真实宿主不是单表，而是 `wh_share_stock + wh_share_flow_record + wh_lm_api_transfer + wh_share_surplus_day_report` 四层组合。
- 当前结论：共享库存运维动作里已经内置了老庙接口推送、失败重试、差异调平和昨日结余核对。
- 新增待确认：关闭共享后按客户全删是否就是预期，还是应该只删本次库位对应维度。
- 新增待确认：流水重试和调平重试为何分成两套机制，是否会导致运维口径不一致。
- 下一优先：继续收共享参与方/共享库位配置这条上游规则链。

## 共享方续补（2026-03-31）
- 已完成模块：共享方
- 新增文档：[shareparty.md](/D:/ws/code/wms-api/docs/business/shareparty.md)
- 新增图： [entity-share-party-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-share-party-chain.md)、[share-party-state.md](/D:/ws/code/wms-api/docs/business/model/share-party-state.md)
- 当前结论：共享方是共享库存的上游规则宿主，不是普通后台主数据。
- 当前结论：配置共享方库位时会继续触发共享库存下发，属于高影响配置动作。
- 新增待确认：共享方 `code`、客户绑定和状态约束是否还有数据库级保护。
- 新增待确认：先删后建的库位设置在失败场景下是否需要补回滚保护。
- 下一优先：继续收老庙共享链剩余模块，优先看共享流水或共享参与方下游接口。

## 共享流水与老庙接口痕迹续补（2026-03-31）
- 已完成模块：共享流水记录、老庙接口调用痕迹
- 新增文档：[shareflowrecord.md](/D:/ws/code/wms-api/docs/business/shareflowrecord.md)、[lmapitransfer.md](/D:/ws/code/wms-api/docs/business/lmapitransfer.md)
- 新增图： [entity-share-flow-lm-api-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-share-flow-lm-api-chain.md)、[share-flow-lm-api-state.md](/D:/ws/code/wms-api/docs/business/model/share-flow-lm-api-state.md)
- 当前结论：共享链的运维闭环是 `flow_record -> lm_api_transfer -> 回写主流水/主库存`，不是单表处理。
- 当前结论：`serialNumber` 是排查共享库存与老庙接口问题的核心串联键。
- 新增待确认：`tryCount`、`callback*` 字段当前是否有其他隐式维护入口。
- 新增待确认：重试多次后 `getOneBySerialNumber(...)` 命中的是否一定是最新痕迹。
- 下一优先：继续收老庙共享链旁路模块，优先看共享比对或老庙订单接口宿主。

## 老庙订单与回调接口续补（2026-03-31）
- 已完成模块：老庙下单接口、老庙回调接口
- 新增文档：[lmorder.md](/D:/ws/code/wms-api/docs/business/lmorder.md)、[lmcallback.md](/D:/ws/code/wms-api/docs/business/lmcallback.md)
- 新增图： [entity-lm-order-callback-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-lm-order-callback-chain.md)、[lm-order-callback-state.md](/D:/ws/code/wms-api/docs/business/model/lm-order-callback-state.md)
- 当前结论：这两组入口本质上都是协议适配层，真正业务分别落在 `customerOrderService`、`syncTaskService`、`deliveryBusiness`。
- 当前结论：回调侧已出现从同步任务链向交付业务链迁移的迹象。
- 新增待确认：老庙接口的签名校验/来源校验是否在网关或别层承担。
- 新增待确认：`syncTaskService` 与 `deliveryBusiness` 之间的回调职责边界是否已经完全稳定。
- 下一优先：继续收老庙相关剩余宿主，优先看同步任务模块。

## 接口同步任务续补（2026-03-31）
- 已完成模块：接口同步任务
- 新增文档：[synctask.md](/D:/ws/code/wms-api/docs/business/synctask.md)
- 新增图： [entity-sync-task-chain.md](/D:/ws/code/wms-api/docs/business/model/entity-sync-task-chain.md)、[sync-task-state.md](/D:/ws/code/wms-api/docs/business/model/sync-task-state.md)
- 当前结论：这张表既记录同步请求，也承接老庙回调处理结果，是对外接口链的任务宿主。
- 当前结论：单品档案、客户订单、结算单、共享相关链路都会把结果回写到这张表。
- 新增待确认：`taskNo` 的时间戳生成规则是否已有数据库唯一约束兜底。
- 新增待确认：`wh_sync_task` 与 `wh_lm_api_transfer` 之间的职责边界是否需要进一步收口。
- 下一优先：继续收对外接口链剩余模块，优先看 stockshare / itemshare 这组历史同步宿主。

## 2026-03-31 会话补充：旧版共享库存链
- 本轮已完成：`itemshare`、`stockshare`、`stocksharelog`。
- 核心判断：这是旧版共享库存体系，和前面已经落盘的 `sharestock/shareflowrecord/shareparty/lmapitransfer` 新体系并存。
- 关键风险：两套共享库存主表 `wh_stock_share` 与 `wh_share_stock` 口径不同，后续若梳理改造，需要先明确哪套是当前业务主口径。
- 下一步继续顺着共享/外部接口残余模块往下收，不停在进度同步点。

## 2026-03-31 会话补充：出货履约链
- 本轮已完成：`deliverybill`。
- 核心判断：`deliverybill` 是客户平台订单的对外履约宿主，不是独立物流台账；真正闭环包含老庙出货回调和共享库存延迟扣减两段。
- 下一步继续沿这条链回到 `customerorder / CustomerOrderIntentBusiness` 上游宿主，不停在进度同步点。

## 2026-03-31 会话补充：客户订单主链
- 本轮已完成：`customerorder`。
- 核心判断：客户订单是客户履约域宿主，老庙建单、现场挑货、销售开单、出货履约、同步任务和共享库存回写都会回落到这张主表或其子表。
- 下一步继续顺着客户订单域旁边的 `customerorderreceive / customerorderkanban / pack` 残余入口往下收，不停在进度同步点。

## 2026-03-31 会话补充：客户订单接单层
- 本轮已完成：`customerorderreceive`。
- 核心判断：接单层是客户订单域的前置流转入口，负责 `UN_SUBMIT / WAIT_TAKING / WAIT_PICKING / WAIT_COMEBACK` 这段状态机，不承接后面的履约回调主链。
- 下一步继续往 `customerorderkanban` 与客户订单域剩余入口推进。

## 2026-03-31 会话补充：客户订单看板
- 本轮已完成：`customerorderkanban`。
- 核心判断：这层是客户订单域的聚合查询层，不是执行单；真正价值在于把 `customer_order_detail` 的计划量与 `customer_order_item` 的实际发货量拼到同一张看板里。
- 下一步继续往剩余未落模块推进，不停在进度同步点。

## 2026-03-31 会话补充：客户共享库位
- 本轮已完成：`customerlocation`。
- 核心判断：这层是共享库存域的上游配置入口，不是静态客户资料子表；一旦改绑定，会继续联动 `sharestock`。
- 下一步继续沿客户域和共享域的高频配置模块往下补。
## 2026-03-31 ?????????
- ??????customermarking?
- ???????????????????????????????????????????????????
- ?????getSimpleList(...) ????????????deleteCustomerMarking(...) ?????????????????
- ????????????????????? customerlock?
## 2026-03-31 ?????????
- ??????customerlock?
- ??????????????????????????? + ??????????? wh_item ? wh_item_log?
- ????????????? customerLock != 0???????????
- ??????????????????????????????????

## 2026-03-31 会话补充：客户字印
- 本轮已完成：customermarking。
- 核心判断：客户字印不是普通附属资料，而是客户订单打印内容、产品包字印校验和默认字印补建链的共同来源池。
- 关键风险：getSimpleList(...) 查询前会自动补默认字印；deleteCustomerMarking(...) 当前又是物理删除，读写边界都偏弱。
- 下一步继续沿客户侧剩余配置层往下收，已接上 customerlock。

## 2026-03-31 会话补充：客锁管理
- 本轮已完成：customerlock。
- 核心判断：这不是独立主表模块，而是单件域上的“客锁查询 + 释放入口”；真实宿主在 wh_item 和 wh_item_log。
- 关键风险：释放逻辑主要只看 customerLock != 0，且日志落点可能重复。
- 下一步继续顺着客户订单执行域和单件运营入口往下补，不停在进度同步点。


## 2026-03-31 会话补充：字印单
- 本轮已完成：markingbill。
- 核心判断：字印单不是普通打印任务，而是“来源单挂接 + 调拨承接 + 执行态流转 + 单件锁定/释放”的宿主链。
- 关键风险：删除当前仍是物理删；marked(...) 会整单把明细改成已字印；单件锁释放链仍需和其他占用链一起核。
- 下一步继续顺着字印执行域往下收 	ray/traydetail。

## 2026-03-31 会话补充：小包
- 本轮已完成：	ray。
- 核心判断：小包不是独立业务单，而是产品包执行域里的包内头表；真正展示还会叠加小包明细、单件实时重量和不干胶配置。
- 关键风险：删除链主要散在控制器里；展示重量不完全是主表快照；SMALL_BAG_STICKER_KEY 会直接影响小包总毛重口径。
- 下一步继续沿这条线收 	raydetail。

## 2026-03-31 会话补充：小包明细与字印单明细
- 本轮已完成：	raydetail、markingdetail。
- 核心判断：这两层都不是独立单据，而是各自主链的 EPC/重量/工费明细宿主；真正的执行追踪要落到明细层看。
- 关键风险：	raydetail 批量更新后未见统一重算头表；markingdetail.deleteMarkingDetailByBillNo(...) 为空实现，getExcleList(...) 当前也返回空列表。
- 下一步继续沿产品包/字印/调拨执行域往下扫剩余未落模块。

## 2026-03-31 会话补充：产品包明细与成品转料明细
- 本轮已完成：packdetail、goodsrecycledetail。
- 核心判断：packdetail 是产品包执行域事实表；goodsrecycledetail 则同时承担成品转料事实层和报表查询层。
- 关键风险：两者删除当前都还是物理删；goodsrecycledetail.listData(...) 还是“明细后拼汇总行”的返回方式。
- 下一步继续沿产品包/转料执行域扫剩余未落卫星层。

## 2026-03-31 会话补充：收款分账明细与订单核销明细
- 本轮已完成：eceivabledividebill、orderwriteoffdetail。
- 核心判断：前者是收款分账结果子表，后者则是订单核销的事实层、报表层和调拨引用层三合一宿主。
- 关键风险：两者删除当前都还是物理删；orderwriteoffdetail 报表总核销量依赖现场重算，eceivabledividebill 展示补数层则几乎为空。
- 下一步继续沿财务和核销主链旁的残余卫星层往下补。

## 2026-03-31 会话补充：配件域
- 本轮已完成：`accessoriesarchives`、`accessoriesclass`、`accessorycategory`、`accessorygoods`、`accessoryapplybill`、`accessoryentrybill`、`accessoryoutbill`、`accessorystock`、`accessorystocklog`。
- 核心判断：配件域已经形成“档案/类别/分类/商品”主数据层，以及“领用/入库/出库/库存/库存日志”执行台账层，但入库/出库审核链目前更像状态流和台账宿主，未明显看到真实库存增减闭环。
- 关键风险：辅料入库/出库审核目前都只见单据状态切换；`AccessoryOutBillServiceImpl.buildConditions(...)` 的 `shopIds` 过滤写错字段，`buildDetailConditions(...)` 的 `payed` 过滤误用了入库单 DO；辅料库存和库存日志当前删除仍是物理删。
- 下一步继续沿剩余未落的基础主数据、库存/报表和财务卫星模块往下扫，不停在进度同步点。

## 2026-03-31 会话补充：资金主数据
- 本轮已完成：`account`、`wallet`、`walletlog`、`shopbank`、`shopbanklog`。
- 核心判断：这组不是薄资料层；`account` 会桥接客户/供应商与钱包，`wallet` 是财务单据真实回写宿主，`shopbank` 才是银行侧余额和转账主账。
- 关键风险：`deleteAccount(...)` 会级联删钱包；`doChangeWallet(...)` 是多条财务链共用核心入口；`transferBalance(...)` 是银行卡双边余额更新和日志生成的关键点。
- 下一步继续往剩余未落的基础主数据、库存/报表和财务卫星模块推进，不停在进度同步点。

## 2026-03-31 会话补充：组织与库存主数据
- 本轮已完成：`shop`、`businessarea`、`businessstaff`、`measureunit`、`material`、`goodsstock`、`goodsstocklog`、`materialstock`、`materialstocklog`。
- 核心判断：`shop/businessarea/businessstaff` 是客户、订单和权限过滤链的组织宿主；`material` 是原料主数据和钱包类型映射宿主；`goodsstock/materialstock` 则分别是成品和原料库存真实更新宿主，不是纯查询表。
- 关键风险：分区与部门建联代码有注释痕迹；成品库存件数校验有注释残留；原料库存 `refreshDaily(...)` 是重要一致性节点；多张主数据表删除仍是物理删。
- 下一步继续往剩余未落的报表、任务、财务卫星和附件模块推进，不停在进度同步点。

## 2026-04-01 会话补充：报表与任务层
- 本轮已完成：`inventorytask`、`printtask`、`printtaskdetail`、`stockreport`、`stockreportdetail`。
- 核心判断：盘点任务层是单件盘点单下的任务池；打印任务层是标签打印执行宿主，并继续回改单件打印状态；即时库存报表层则是由 controller + `skuStockDailyService/reportAllService` 共同驱动的聚合报表宿主。
- 关键风险：盘盈池保存采用先删后建；打印任务审核时间写入和重置边界需要确认；即时库存报表 service 里存在空返回和存在性校验缺失。
- 下一步继续往剩余未落的报表、财务卫星和附件/配置模块推进，不停在进度同步点。

## 2026-04-01 会话补充：结算与调拨明细层
- 本轮已完成：`accounttransferbill`、`demandorderdetail`、`salesettle`、`returnsettle`、`transferbilldetail`、`goodstransferbilldetail`。
- 核心判断：`accounttransferbill` 不是薄财务单，而是审核即回写双钱包的结价执行宿主；`demandorderdetail` 不是普通从表，而是需求单域真正的分页、汇总和委外聚合宿主；销售/退货/调拨三条主链的 detail 层也都各自承接了真实事实和导出查询。
- 关键风险：结价单删除仍是物理删，需求单明细按单头整批删，退货结算单条与列表回填口径不一致，单件调拨导出尾行会手工拼汇总。
- 下一步继续往剩余未独立落盘的轻量配置层和附件/日志残余模块扫，不停在进度同步点。

## 2026-04-01 会话补充：财务明细卫星层
- 本轮已完成：`receivablebilldetail`、`payablebilldetail`、`customeradjustbilldetail`、`supplieradjustbilldetail`、`accountadjustbilldetail`、`adjustbilldetail`。
- 核心判断：主单负责审核和钱包/库存动作，detail 层则承接分笔事实、分页、导出和展示补数；其中 `adjustbilldetail` 最重，还继续挂配件卫星和 SKU 补数。
- 关键风险：收款/付款 detail 导出都会手工追加汇总行；客户应收调整 detail 权限过滤里 `wareIds` 未继续生效；单件调整 detail 强依赖 SKU 主数据完整。
- 下一步继续往剩余未独立落盘的库存/维修/来料出料 detail、日志和配置卫星层推进，不停在进度同步点。

## 本轮新增（执行明细与日志卫星层）
- 新增模块文档：`entrybilldetail`、`materialinbounddetail`、`materialoutbounddetail`、`returndetail`、`goodsinboundbilldetail`、`maintaininbilldetail`、`maintaininbilldetaillog`、`maintainoutbilldetail`
- 新增图：`execution-detail-state`、`entity-execution-detail-chain`

## 本轮结论（执行明细与日志卫星层）
- `entrybilldetail` 是单件入库审核后新单件身份回填层。
- `materialinbounddetail/materialoutbounddetail` 承接库存口径与钱包口径两套重量汇总。
- `returndetail/goodsinboundbilldetail` 都是报表事实层，不只是子表。
- `maintaininbilldetaillog` 会自动生成客户留货，不是被动日志。
- `maintainoutbilldetail` 负责重复维修与后续时序校验。

## 本轮新增风险（执行明细与日志卫星层）
- `materialoutbounddetail.fillData(...)` 存在 `walletDO` 空指针风险。
- 多个 detail 列表/导出都会手工追加总计行。
- `maintaininbilldetail` 的“最近一次结果”是按最大 id 推断。
- `maintaininbilldetaillog` 失败会影响客留单自动生成。

## 本轮新增（单件日志、盘点卫星与 SKU 库存层）
- 新增模块文档：`itemlog`、`iteminventorybillitem`、`iteminventorybillstock`、`skustock`、`skustockdaily`
- 新增图：`item-stock-satellite-state`、`entity-item-stock-satellite-chain`

## 本轮结论（单件日志、盘点卫星与 SKU 库存层）
- `itemlog` 是单件域事件宿主。
- `iteminventorybillitem` 承接差异页、统计页和盘盈盘亏派生逻辑。
- `skustock` 不是报表层，而是实时 SKU 库存执行宿主。
- `skustockdaily` 采用先删后建初始化日报。

## 本轮新增风险（单件日志、盘点卫星与 SKU 库存层）
- `itemlog` 删除仍是物理删除，历史查询还会回查当前 `wh_item` 补字段。
- `iteminventorybillitem` 统计口径依赖硬编码大类编号。
- `skustock.refreshSingleDaily(...)` 有 3 个月追溯限制。
- `skustockdaily.initDaily()` 失败会影响整日日报。

## 本轮新增（明细事实层与资金日结卫星）
- 新增模块文档：`customerorderdetail`、`goodsrejectbilldetail`、`goodsrejectrecorddetail`、`inventoryweighingdetail`、`profitlossadjustdetail`、`wastagedetail`、`walletdaily`、`shopbankdaily`
- 新增图：`detail-daily-satellite-state`、`entity-detail-daily-satellite-chain`

## 本轮结论（明细事实层与资金日结卫星）
- `customerorderdetail` 是客户订单行需求和看板聚合宿主。
- `goodsrejectbilldetail/goodsrejectrecorddetail` 分别承接退厂报表事实与退厂登记池明细。
- `walletdaily/shopbankdaily` 都是日结和差异核对宿主。

## 本轮新增风险（明细事实层与资金日结卫星）
- `customerorderdetail.getKanbanDetail(...)` 的 `skuNo` 过滤字段疑似写错。
- 多个导出明细层会手工追加总计行。
- `walletdaily` 有 3 个月追溯限制。
- `shopbankdaily.genShopBankDaily()` 采用先删后建。

## 本轮新增（规则、日志与记录尾项）
- 新增模块文档：`inrejectreason`、`skurulelog`、`skusearchlog`、`stockmessagerecord`、`shareflowrecord`、`lmapitransfer`
- 新增图：`rule-log-record-state`、`entity-rule-log-record-chain`

## 本轮结论（规则、日志与记录尾项）
- `inrejectreason` 是入库/退厂报表共同依赖的原因主数据。
- `skurulelog` 承接 SKU 编码落号历史。
- `stockmessagerecord/shareflowrecord/lmapitransfer` 都是异步或接口链的痕迹宿主。

## 本轮新增风险（规则、日志与记录尾项）
- `skusearchlog` 的存在性校验异常被注释掉了。
- `shareflowrecord.updateById(...)` 返回值恒为 `null`。
- `lmapitransfer.getOneBySerialNumber(...)` 只取一条记录。

## 本轮新增（执行尾项明细）
- 新增模块文档：`cancelbilldetail`、`accessoryapplybilldetail`、`distributionbilldetail`、`shelvesbilldetail`、`unitpricemaintaindetail`
- 新增图：`execution-tail-detail-state`、`entity-execution-tail-detail-chain`

## 本轮结论（执行尾项明细）
- `cancelbilldetail` 是单件作废链事实层。
- `distributionbilldetail` 是铺货链明细与来源挂接宿主。
- `shelvesbilldetail` 需要按 EPC 回查单件补数。

## 本轮新增风险（执行尾项明细）
- 这批 detail 删除基本都是物理删除。
- `distributionbilldetail` 导出路径有单头缺失空指针风险。
- `shelvesbilldetail` 的历史展示受当前单件状态影响。

## 本轮新增（半成品、转料与客留尾项）
- 新增模块文档：`recycledetail`、`semistransfer`、`materialinventorydetail`、`customerretain`
- 新增图：`semi-retain-satellite-state`、`entity-semi-retain-satellite-chain`

## 本轮结论（半成品、转料与客留尾项）
- `recycledetail` 是单件转料事实层。
- `semistransfer` 是半成品调拨完整主链，不是薄 service。
- `customerretain` 是客户留货业务宿主链。

## 本轮新增风险（半成品、转料与客留尾项）
- `semistransfer.modify(...)` 采用删旧后重建模式。
- `customerretain.transfer(...)` 的部分迁转会拆成两条记录。
- `customerretain` 删除仍是物理删除。

## 本轮新增（价格、盘点与铺货尾项）
- 新增模块文档：`distributionbill`、`recyclebill`、`inventorybill`、`materialinventory`、`materialinboundsettle`、`unitpricemaintain`、`unitprice`、`walletcredit`
- 新增图：`pricing-inventory-tail-state`、`entity-pricing-inventory-tail-chain`

## 本轮结论（价格、盘点与铺货尾项）
- `distributionbill` 是铺货执行主链。
- `recyclebill` 是单件转料执行主链。
- `unitpricemaintain` 会实时回写正式 `unitprice` 主档。
- `walletcredit` 是账户授信额度主档。

## 本轮新增风险（价格、盘点与铺货尾项）
- `distributionbill` 与 `recyclebill` 改单都采用删旧重建。
- `inventorybill` 没看到正式库存过账。
- `unitprice.getGoldUnitPrice()` 失败直接返回 `0`。
- `walletcredit` 采用一账户一授信建模。

## 本轮新增（商品与 SKU 尾项）
- 新增模块文档：`categoryfour`、`categoryfive`、`categoryspec`、`bigweightsegment`、`skufavorite`、`skuseriesmapping`、`skusetmapping`、`skugemstone`、`skuparts`
- 新增图：`sku-tail-master-state`、`entity-sku-tail-master-chain`

## 本轮结论（商品与 SKU 尾项）
- `categoryfour/categoryfive/categoryspec` 都是商品结构链主数据。
- `bigweightsegment` 是重量段规则主数据。
- `skufavorite` 是收藏宿主链。
- `skusetmapping` 会回写 SKU 主档的套式信息。

## 本轮新增风险（商品与 SKU 尾项）
- 这批主数据删除基本都是物理删除。
- `skufavorite.favorite(...)` 是 toggle 语义。
- 系列映射和套式映射的存在性校验异常都被注释掉了。
- `skusetmapping.deleteSkuSetMapping(...)` 不会回滚 SKU 主档套式字段。

## 本轮新增（客户与组织尾项）
- 新增模块文档：`customercontacts`、`customerstaffupdate`、`customerstaffupdatedetail`、`businessareadept`、`skushop`、`exhibition`、`qcstaff`
- 新增图：`customer-org-tail-state`、`entity-customer-org-tail-chain`

## 本轮结论（客户与组织尾项）
- `customercontacts` 已经是客户小程序登录入口层，不只是联系人主数据。
- `customerstaffupdate` 是客户归属整体迁移宿主，且挂了 BPM。
- `customerstaffupdatedetail` 承接前后归属差异补数视图。
- `skushop` 是 SKU 网点侧关系和费用索引。
- `exhibition` 会固化客户归属快照。
- `qcstaff` 是质检员主数据。

## 本轮新增风险（客户与组织尾项）
- `customercontacts.getByOpenId(openId)` 的过期过滤结果看起来没有真正生效。
- `customerstaffupdate` 跨很多业务表，后续加字段很容易漏接。
- `customerstaffupdate` 改单是删旧明细后重建。
- 这批尾项主数据删除目前基本都是物理删除。

## 本轮新增（客户订单单件与原料品质尾项）
- 新增模块文档：`customerorderitem`、`itemsale`、`materialquality`
- 新增图：`order-item-material-tail-state`、`entity-order-item-material-tail-chain`

## 本轮结论（客户订单单件与原料品质尾项）
- `customerorderitem` 是客户订单履约的单件事实层。
- `itemsale` 是单件销售落点卫星层。
- `materialquality` 是原料品质规则主数据。

## 本轮新增风险（客户订单单件与原料品质尾项）
- `customerorderitem.fillData(...)` 依赖当前 `wh_item` 补数。
- `customerorderitem.getKanbanItem(...)` 里的 `skuNo` 过滤疑似串到了 `modelNo`。
- `itemsale.removeByItemIds(...)` 是物理删除。
- `materialquality` 多套编码字段的唯一性保护逻辑还需要确认。

## 本轮新增（资金与消息尾项）
- 新增模块文档：`goldpricesetting`、`internalremittance`、`ordermessage`
- 新增图：`finance-message-tail-state`、`entity-finance-message-tail-chain`

## 本轮结论（资金与消息尾项）
- `goldpricesetting` 是金价配置、规则和日志组合宿主。
- `internalremittance` 是银行卡内部转账主链。
- `ordermessage` 是客户订单提醒与待办宿主。

## 本轮新增风险（资金与消息尾项）
- `goldpricesetting` 更新后写日志的 `settingId` 回填边界需要继续确认。
- `internalremittance.update(...)` 采用删旧明细后重建。
- `ordermessage.deleteOrderMessage(...)` 是物理删除。
- `ordermessage.convertByCustomerOrderId(...)` 的消息接收人口径会受客户当前归属影响。

## 本轮新增（盘点批次与历史库存尾项）
- 新增模块文档：`inventoryweighingbillstock`、`iteminventorybillbatch`、`itemhistorystock`
- 新增图：`inventory-history-tail-state`、`entity-inventory-history-tail-chain`

## 本轮结论（盘点批次与历史库存尾项）
- `inventoryweighingbillstock` 是盘点称重的库存备份快照池。
- `iteminventorybillbatch` 是单件盘点批次宿主。
- `itemhistorystock` 是单件每日库存和重量段汇总的历史快照宿主。

## 本轮新增风险（盘点批次与历史库存尾项）
- `inventoryweighingbillstock.initInventoryWeighingBillStock(...)` 采用先删后建。
- `iteminventorybillbatch.deleteItemInventoryBillBatch(...)` 是物理删除。
- `itemhistorystock.snapshot(...)` 和 `summaryBySegment(...)` 都采用先删后建。
- `itemhistorystock` 的重量段区间目前是硬编码的。

## 本轮新增（辅石与配件卫星尾项）
- 新增模块文档：`itemaccessories`、`entrybilldetailaccessories`、`supplierskuaccessories`、`supplierskupart`
- 新增图：`accessories-satellite-tail-state`、`entity-accessories-satellite-tail-chain`

## 本轮结论（辅石与配件卫星尾项）
- `entrybilldetailaccessories` 是单件入库明细辅石快照层。
- `itemaccessories` 是正式单件辅石事实层。
- `supplierskuaccessories` / `supplierskupart` 是供应商 BOM 的辅石和配件卫星层。

## 本轮新增风险（辅石与配件卫星尾项）
- `itemaccessories.getListByEpcs(...)` 方法名和真实字段口径有偏差。
- `entrybilldetailaccessories` 与 `itemaccessories` 字段高度镜像，后续容易漂移。
- `supplierskuaccessories` / `supplierskupart` 的清理都是物理删除。

## 本轮新增（分析报表尾项）
- 新增模块文档：`supplierqualityreport`、`departmentlossreport`
- 新增图：`analysis-report-tail-state`、`entity-analysis-report-tail-chain`

## 本轮结论（分析报表尾项）
- `supplierqualityreport` 是供应商品质分析结果宿主。
- `departmentlossreport` 是部门折损分析结果宿主。
- 这两条链的展示口径都统一收敛在 `SkuReportBusiness`。

## 本轮新增风险（分析报表尾项）
- 这两条报表链的 controller 实际都走 `SkuReportBusiness`，service 不是完整入口。
- 聚合维度分支是硬编码常量判断。
- `departmentlossreport.getLossTotal(...)` 只按 `reportDate` 汇总。

## 本轮新增（预处理与占料分析尾项）
- 新增模块文档：`saleprepossessing`、`reportcustomerareaowe`
- 新增图：`preprocess-owe-tail-state`、`entity-preprocess-owe-tail-chain`

## 本轮结论（预处理与占料分析尾项）
- `saleprepossessing` 是订单结算前预处理宿主。
- `reportcustomerareaowe` 是客户/分区日均占料分析报表壳。

## 本轮新增风险（预处理与占料分析尾项）
- `saleprepossessing.confirm/infirm/discard` 的状态判断写法看起来有问题。
- `saleprepossessing.deleteSalePrepossessing(...)` 是物理删除。
- `reportcustomerareaowe` 主入口在 controller 存储过程链，不是 service 普通分页。
- `handleGroupByArea(...)` 里 `mentalToday` 被重复 set。

## 本轮新增（柜台与周转率报表尾项）
- 新增模块文档：`reporttransferwarehouse`、`salereportware`
- 新增图：`report-ware-tail-state`、`entity-report-ware-tail-chain`

## 本轮结论（柜台与周转率报表尾项）
- `reporttransferwarehouse` 是库存销售周转率报表壳。
- `salereportware` 是多维销售统计报表壳。

## 本轮新增风险（柜台与周转率报表尾项）
- `reporttransferwarehouse` 的主入口在 controller 存储过程链。
- `salereportware` 的 controller 承担了大量真实口径处理。
- 这两条报表链当前删除都还是物理删除。
