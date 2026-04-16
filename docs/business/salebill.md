# 出库结算单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责出库结算单的创建、修改、审核、反审、作废、发货申请、确认可发货、实际发货、重做，以及调拨中的产品包接收/拒收/退回；同时联动产品包、客户订单、客留存、钱包和出货单。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_sale_bill | 读写 | 出库结算单主表，对应 `SaleBillDO` |
| wh_sale_detail | 读写 | 出库结算明细表，对应 `SaleDetailDO` |
| wh_sale_tray | 读写 | 单件结算小包/托盘明细 |
| wh_sale_settle | 读写 | 结算方式明细，对应 `SaleSettleDO` |
| wh_pack | 读写 | 结算时回写销售单号、状态、调拨状态 |
| wh_customer_order_item | 读写 | 结算时推进订单明细本地状态 |
| wh_customer_retain / wh_customer_retain_record | 读写 | 实际发货后结留存、重做时回滚 |

## 模块依赖
- `PackService`：校验产品包、回写销售单号、推进/回滚产品包状态
- `CustomerOrderService`：推进订单明细 `SETTLEMENT/BILLED`，实际发货后执行 `orderShipment/orderShipmentBack`
- `CustomerRetainService`：实际发货后执行 `retainSettled`，重做时执行 `retainSettledBack`
- `TransferBillService / GoodsTransferBillService`：处理结算单关联产品包的调拨接收、拒签、退回
- `DeliveryBillService` / `genDeliveryBill()`：实际发货后生成出货单
- `Wallet` 相关服务：审核/反审时更新客户钱包并写日志

## 接口列表

### createSaleBill
> `SaleBillController.java#createSaleBill()` `→` `SaleBillBusiness.java#create()` `→ L115 / L221`

- **用途**：创建出库结算单、结算方式明细和结算明细。
- **前置条件**：
  - 非费用单时，表头 `bigClassId` 对应大类必须存在
  - 结算明细中引用的产品包必须通过表头大类、结算仓、材质存欠校验
- **业务逻辑**：
  1. 生成结算单号。
  2. 填充客户、业务员、仓库等表头信息。
  3. 按业务类型把 `trayList` 转成 `detailList`。
  4. 校验来源产品包和表头大类/材质/结算仓一致。
  5. 汇总结算金额、金重、件数，构造 `wh_sale_bill`。
  6. 写入 `wh_sale_bill`、`wh_sale_settle`、`wh_sale_detail/wh_sale_tray`。
  7. 调用 `customerOrderItemStatusChange(packIds, SETTLEMENT)`，把相关订单明细推进到结算中。
  8. 调用 `packsConnectSale(saleBill, packIds)`，把产品包挂到当前结算单。
- **状态变更**：
  - `wh_customer_order_item.local_status -> SETTLEMENT`
  - `wh_pack.sale_bill_id / sale_bill_no` 被写入当前结算单

### updateSaleBill
> `SaleBillController.java#updateSaleBill()` `→` `SaleBillBusiness.java#update()` `→ L124 / L722`

- **用途**：修改未审或已反审的结算单。
- **前置条件**：
  - `id` 非空
  - 原单状态必须满足 `SaleBillStatusEnum.allowModifyOrConfirm()`，即 `INIT/INFIRM`
- **业务逻辑**：
  1. 读取旧单并校验可修改。
  2. 重新按请求内容构造表头、结算方式和明细。
  3. 删除旧 `wh_sale_settle`、旧明细/托盘后重建。
  4. 重新把相关订单明细推进到 `SETTLEMENT`。
  5. 重新回写产品包与结算单关联。

### saveAndConfirm
> `SaleBillController.java#saveAndConfirm()` `→` `SaleBillBusiness.java#saveAndConfirm()` `→ L132 / L1195`

- **用途**：一键保存并审核。
- **业务逻辑**：
  1. 无 `id` 时先 `create()`。
  2. 有 `id` 时先 `update()`。
  3. 再调用 `confirm(id)`。

### confirm
> `SaleBillController.java#confirm()` `→` `SaleBillBusiness.java#confirm()` `→ L157 / L791`

- **用途**：审核结算单。
- **前置条件**：
  - 单据状态必须是 `INIT/INFIRM`
  - 客户存在
  - `wh_sale_settle` 明细非空
  - 结价方式 `settleType=1` 时，`goldPrice != 0`
  - 结算金重合计必须等于产品包金重合计
- **业务逻辑**：
  1. 校验客户、结算方式、明细和结算重量。
  2. 更新 `wh_sale_bill.bill_status = CONFIRM`，并写入审核人/审核时间。
  3. 更新客户钱包和钱包日志。
  4. 将关联产品包状态推进到 `PackStatusEnum.RECEIVED`。
  5. 调用 `customerOrderItemStatusChange(packIds, BILLED)`，把订单明细推进到已开票/已结算阶段。
  6. 处理维修单关联数量。
- **状态变更**：
  - `wh_sale_bill.bill_status: INIT/INFIRM -> CONFIRM`
  - `wh_pack.pack_status -> RECEIVED`
  - `wh_customer_order_item.local_status -> BILLED`

### unConfirm
> `SaleBillController.java#unConfirm()` `→` `SaleBillBusiness.java#infirm()` `→ L166 / L1059`

- **用途**：反审已审或已审未发货的结算单。
- **前置条件**：
  - 结算单调拨状态不能处于调拨中
  - 单据状态必须满足 `allowInfirm()`，即 `CONFIRM/CONFIRM_NO_DELIVERY`
  - 跨日反审需要权限 `PERMIT_SALE_CROSS_DAYS`
- **业务逻辑**：
  1. 更新 `wh_sale_bill.bill_status = INFIRM`。
  2. 反向更新客户钱包和钱包日志。
  3. 将产品包状态还原为 `PackStatusEnum.CONFIRM`。
  4. 调用 `customerOrderItemStatusChange(packIds, SETTLEMENT)`，订单明细回到结算中。
  5. 回退维修单关联数量。
- **状态变更**：
  - `wh_sale_bill.bill_status: CONFIRM/CONFIRM_NO_DELIVERY -> INFIRM`
  - `wh_pack.pack_status -> CONFIRM`
  - `wh_customer_order_item.local_status -> SETTLEMENT`

### obsolete
> `SaleBillController.java#obsolete()` `→` `SaleBillBusiness.java#discard()` `→ L245 / L1168`

- **用途**：作废未审或已反审结算单。
- **前置条件**：
  - 调拨状态不能处于调拨中
  - 状态必须满足 `allowDiscard()`，即 `INIT/INFIRM`
- **业务逻辑**：
  1. 读取关联产品包。
  2. 调用 `packsConnectSale(saleBill, emptySet)` 取消产品包与销售单关联。
  3. 调用 `customerOrderItemStatusChange(packIds, BILLING)`，把订单明细退回开单态。
  4. 更新 `wh_sale_bill.bill_status = DISCARD`。
- **状态变更**：
  - `wh_sale_bill.bill_status: INIT/INFIRM -> DISCARD`
  - `wh_customer_order_item.local_status -> BILLING`

### deliveryRequest / deliveryRequest(ids)
> `SaleBillController.java#deliveryRequest()` `→` `SaleBillServiceImpl.java#deliveryRequest()` `→ L269 / L379`
> `SaleBillController.java#deliveryRequest(Long[])` `→` `SaleBillServiceImpl.java#deliveryRequests()` `→ L285 / L392`

- **用途**：单张或批量申请发货。
- **前置条件**：结算单状态必须是 `CONFIRM`。
- **业务逻辑**：
  1. 单张接口把 `wh_sale_bill.bill_status` 更新为 `DELIVERY_ALLOW`。
  2. 批量接口逐张调用单张逻辑。
- **状态变更**：
  - `wh_sale_bill.bill_status: CONFIRM -> DELIVERY_ALLOW`

### delivered
> `SaleBillController.java#delivered()` `→` `SaleBillServiceImpl.java#delivered()` `→ L304 / L433`

- **用途**：确认可发货。
- **前置条件**：单据状态必须是 `DELIVERY_ALLOW`。
- **状态变更**：
  - `wh_sale_bill.bill_status: DELIVERY_ALLOW -> CONFIRM_ALLOW_DELIVERY`

### actualDelivered / actualDelivers
> `SaleBillController.java#actualDelivered()` `→` `SaleBillBusiness.java#actualDelivered()` `→ L314 / L1242`
> `SaleBillController.java#actualDelivers()` `→` `SaleBillBusiness.java#actualDelivers()` `→ L322 / L1489`

- **用途**：确认实际已发货，单张或批量执行。
- **前置条件**：结算单状态必须是 `CONFIRM_ALLOW_DELIVERY`。
- **业务逻辑**：
  1. 更新 `wh_sale_bill.bill_status = DELIVERED`。
  2. 读取结算明细并按产品包分组。
  3. 扣减单件/成品库存。
  4. 调用 `customerRetainService.retainSettled(billNo, packList)`，把客留存推进为已结留存。
  5. 调用 `customerOrderService.orderShipment(saleBillId, orderItemIds)`，推进订单出货信息。
  6. 生成出货单。
- **状态变更**：
  - `wh_sale_bill.bill_status: CONFIRM_ALLOW_DELIVERY -> DELIVERED`

### staffNoDelivered
> `SaleBillController.java#staffNoDelivered()` `→` `SaleBillServiceImpl.java#staffNoDelivered()` `→ L332 / L849`

- **用途**：业务确认不发货。
- **前置条件**：单据状态必须是 `DELIVERY_ALLOW`。
- **状态变更**：
  - `wh_sale_bill.bill_status: DELIVERY_ALLOW -> CONFIRM_NO_DELIVERY`

### orderRedo
> `SaleBillController.java#orderRedo()` `→` `SaleBillBusiness.java#cancel()` `→ L358 / L1496`

- **用途**：结算重做，把已发货或确认不发货的单据退回已审。
- **前置条件**：
  - 状态必须是 `DELIVERED` 或 `CONFIRM_NO_DELIVERY`
  - 跨日重做需要权限 `SALE_BILL_CANCEL_CROSS_DAYS`
- **业务逻辑**：
  1. 把结算单状态改回 `CONFIRM`。
  2. 若原状态是 `DELIVERED`，则执行库存回滚 `stockIn()`。
  3. 调用 `retainSettledBack(billNo, packList)`，重激活客留存。
  4. 调用 `orderShipmentBack(saleBillId)`，回滚客户订单出货信息。
- **状态变更**：
  - `wh_sale_bill.bill_status: DELIVERED/CONFIRM_NO_DELIVERY -> CONFIRM`

### receiveTransfer / rejectTransfer / returningTransfer / receivedPacks
> `SaleBillController.java#receiveTransfer()` `→` `SaleBillBusiness.java#receiveTransfer()` `→ L491 / L1209`
> `SaleBillController.java#rejectTransfer()` `→` `SaleBillBusiness.java#rejectTransfer()` `→ L500 / L1220`
> `SaleBillController.java#returningTransfer()` `→` `SaleBillBusiness.java#returningTransfer()` `→ L509 / L1231`
> `SaleBillController.java#receivedPacks()` `→` `SaleBillBusiness.java#receivePacks()` `→ L574 / L1809`

- **用途**：处理结算单关联产品包的调拨接收、拒签、退回、批量接收。
- **业务逻辑**：
  1. 先读取产品包 `bizType`。
  2. 单件包走 `TransferBillService`。
  3. 成品包走 `GoodsTransferBillService`。
- **回填说明**：
  - 这几组接口本身不直接改结算单主状态，主要驱动关联调拨单状态变化，并由调拨模块回写产品包/结算单的 `transferStatus`。

### financeUpdate / financeUpdateConfirm
> `SaleBillController.java#financeUpdate()` `→` `SaleBillBusiness.java#financeUpdate()` `→ L582 / L4084`
> `SaleBillController.java#financeUpdateConfirm()` `→` `SaleBillBusiness.java#financeUpdateConfirm()` `→ L590 / L4095`

- **用途**：财务改单与财务改单后一键重新走完审核到实际发货。
- **业务逻辑**：
  1. `financeUpdate(id)` 依次调用 `cancel(id)`、`infirm(id)`，并把 `updateLock = true`。
  2. `financeUpdateConfirm(id)` 依次调用 `confirm(id)`、`deliveryRequest(id)`、`delivered(id)`、`actualDelivered(id)`，最后把 `updateLock = false`。

## 公共规则

### 结算单主状态流
- `INIT/INFIRM -> CONFIRM`
- `CONFIRM -> DELIVERY_ALLOW`
- `DELIVERY_ALLOW -> CONFIRM_ALLOW_DELIVERY`
- `DELIVERY_ALLOW -> CONFIRM_NO_DELIVERY`
- `CONFIRM_ALLOW_DELIVERY -> DELIVERED`
- `CONFIRM/CONFIRM_NO_DELIVERY -> INFIRM`
- `INIT/INFIRM -> DISCARD`
- `DELIVERED/CONFIRM_NO_DELIVERY -> CONFIRM`（重做）

### 对产品包和订单的联动
- 创建/修改结算单时：订单明细 `-> SETTLEMENT`，产品包挂销售单号。
- 审核结算单时：产品包 `-> RECEIVED`，订单明细 `-> BILLED`。
- 反审结算单时：产品包 `-> CONFIRM`，订单明细 `-> SETTLEMENT`。
- 作废结算单时：取消产品包挂单，订单明细 `-> BILLING`。
- 实际发货时：结留存、推订单发货、生成出货单。
- 重做时：回滚库存、回滚客留存、回滚订单出货。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deliveryRequest` | 出库结算单 | 接口名叫“发货申请”，状态枚举里也有 `DELIVERY_REQUESTING`，但实现直接把状态改成 `DELIVERY_ALLOW` | 待确认 | 人工确认 `DELIVERY_REQUESTING` 是否已废弃 |
| ⚠️ | `staffNoDelivered` | 出库结算单 | 先执行 `updateByIdAndStatus()`，随后又直接执行一次 `updateById()`，存在重复更新 | 待确认 | 确认第二次更新是否仅为兜底，还是遗留冗余逻辑 |

## 当前结论
- 结算单模块是产品包之后的核心主链，真正的“出货完成”发生在 `actualDelivered()`，不是在审核或确认可发货阶段。
- 它对上下游的影响很强：向前承接产品包和客户订单，向后生成出货单，同时还联动客户钱包、客留存和库存。
