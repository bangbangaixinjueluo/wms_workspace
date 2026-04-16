# 产品包模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责产品包的创建、修改、审核、反审、作废、退回处理，以及基于产品包生成并审核调拨单；同时维护产品包明细、小包数据，并驱动客留存与客户订单状态变化。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_pack | 读写 | 产品包主表，对应 `PackDO` |
| wh_pack_detail | 读写 | 产品包明细表，对应 `PackDetailDO` |
| wh_tray | 读写 | 单件产品包的小包表，对应 `TrayDO` |

## 模块依赖
- `CustomerRetainService`：创建时校验和消耗客留存，作废时回退客留存，预检查时核对客留存剩余明细。
- `CustomerOrderService`：更新订单明细的 `packId` 和本地状态，并触发客户订单状态重算。
- `TrayService`：单件产品包创建和更新时重建小包；审核时校验小包复核毛重。
- `TransferBillService` / `GoodsTransferBillService`：根据产品包生成调拨单，或处理退回拒签、退回接收。
- `DistributionBillDetailService`：审核时按 EPC 更新展销明细。
- `MaintainBillBusiness`：审核和反审时按指令增减维修单关联数量。
- `ItemService` / `SkuStockService` / `ItemLogService`：初始化客留存时执行单件锁定或成品库存检查。

## 接口列表

### createPack
> `PackController.java#createPack()` `→` `PackBusiness.java#create()` `→ L116 / L475`

- **入口类型**：HTTP 接口
- **用途**：创建产品包主单、产品包明细，以及单件产品包的小包信息。
- **入参**：
  - `PackCreateReqVO` - 请求对象 - 包含主单字段、`detailList`/`goodsList`、`trayList`、`retainIds`
- **出参**：
  - `Long` - 产品包 ID
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.create()`。
- **业务逻辑**：
  1. 若 `packNo` 为空则生成单号 `IBC...`。
  2. 校验字印、客户存在、客户已分配业务员。
  3. 校验所选客留存均属于当前客户，且字印信息与请求一致。
  4. 汇总明细和小包，计算主单总件数、总重、总工费。
  5. 写入 `wh_pack`。
  6. 将明细按“已有客留存记录 / 需初始化客留存”拆分，必要时初始化新的客留存和客户订单。
  7. 构造产品包明细并写入 `wh_pack_detail`。
  8. 若业务类型为单件包，则校验并写入 `wh_tray`。
  9. 对关联客留存执行 `packing`：校验打包件数和金重必须分别等于客留存件数和金重，然后将 `wh_customer_retain.pack_no` 更新为当前产品包单号，并将 `wh_customer_retain.status` 从 `STORAGE` 推进到 `ONGOING`。
  10. 把相关客户订单明细更新为 `BILLING`，最后重算客户订单状态。
- **前置条件**：
  - `customerId` 对应客户存在。
  - 客户已配置 `businessStaffId`。
  - 若传入 `retainIds`，其所属客户和字印必须与当前请求一致。
- **状态变更**：
  - `wh_pack` 新增主单记录。
  - `wh_pack_detail` 新增明细记录。
  - `wh_tray` 新增小包记录（仅单件包）。
  - `wh_customer_retain.status`：`STORAGE -> ONGOING`
  - `wh_customer_retain.pack_no`：`null -> {packNo}`
- **异常情况**：
  - 客户不存在。
  - 客户未分配业务员。
  - 客留存所属客户不一致。
  - 客留存字印信息不一致。
  - 单件包存在明细但未提供小包信息。
- **回填说明**：
  - `customerOrderService.customerOrderStatusChange()` 已确认：若订单明细存在 `RETAIN` 则订单进入 `WAIT_SETTLE`；存在 `BILLING/SETTLEMENT` 则进入 `SETTLING`；存在 `BILLED` 则进入 `SETTLED`；全部 `SHIPPED` 则进入 `DELIVERED`；无明细或部分已发货则回到 `WAIT_PICKING`。若订单当前状态为 `UN_SUBMIT`、`WAIT_TAKING`、`WAIT_COMEBACK`、`DISCARD`，则本次不重算。

### updatePack
> `PackController.java#updatePack()` `→` `PackBusiness.java#update()` `→ L125 / L911`

- **入口类型**：HTTP 接口
- **用途**：修改未审或已反审的产品包，并重建其明细、小包和客留存关联。
- **入参**：
  - `PackUpdateReqVO` - 请求对象 - 在创建入参基础上增加 `id`
- **出参**：
  - `Boolean` - 是否成功
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.update()`。
- **业务逻辑**：
  1. 校验 `id` 非空，读取旧产品包。
  2. 仅当旧状态为 `INIT` 或 `INFIRM` 时允许修改。
  3. 若客户、网点、仓库、材质、大类任一变化，则回收旧客留存：将旧 `packNo` 关联的客留存状态更新为 `RECYCLED`、清空 `packNo`、回写当时金价、设置 `endTime` 和 `dayCount`；同时解锁相关 EPC、清理旧订单明细关联，并将当前请求的 `retainRecordId/orderItemId` 清空。
  4. 重新校验字印、客户、客留存。
  5. 重算产品包汇总信息。
  6. 删除旧小包与旧产品包明细，重建新小包和新明细。
  7. 先解绑旧订单明细，再按新的 `retainIds` 重新绑定 `packId` 和 `BILLING` 状态。
  8. 若主数据未变化，仅执行客留存解绑，则将旧 `packNo` 关联的客留存状态恢复为 `STORAGE` 并清空 `packNo`。
  9. 更新 `wh_pack` 主单字段。
- **前置条件**：
  - `id` 必填。
  - 旧状态必须是未审或已反审。
- **状态变更**：
  - `wh_pack` 按 ID 更新。
  - `wh_pack_detail` 先删后插。
  - `wh_tray` 先删后插（仅单件包）。
  - `wh_customer_retain.status`：数据范围变化时 `* -> RECYCLED`；仅解绑时 `* -> STORAGE`
  - `wh_customer_retain.pack_no`：旧产品包号被清空
- **异常情况**：
  - `id` 为空。
  - 产品包状态不是未审或已反审。
  - 客户不存在。
  - 客户未分配业务员。
  - 不支持的产品包业务类型。

### confirm
> `PackController.java#confirm()` `→` `PackBusiness.java#confirm()` `→ L152 / L1150`

- **入口类型**：HTTP 接口
- **用途**：审核产品包。
- **入参**：
  - `id` - `Long` - 产品包 ID
- **出参**：
  - `Boolean` - 是否成功
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.confirm()`。
- **业务逻辑**：
  1. 校验产品包存在，且状态为 `INIT` 或 `INFIRM`。
  2. 校验该产品包已关联客留存，且存在产品包明细。
  3. 单件包校验每个小包 `totalGrossWeightRepeat >= totalGrossWeight`；成品包校验每条明细 `grossWeightRepeat >= goldWeight`。
  4. 调用 `distributionBillDetailService.updateDetailsInfoByEpcs()` 更新展销明细。
  5. 更新 `wh_pack.pack_status = CONFIRM`，并写入 `approvedTime`、`approvedMan`、`packDate`。
  6. 对明细中的 `customerOrderNo` 异步发起同步任务。
  7. 若明细关联维修单，则增加维修单已打包数量。
- **前置条件**：
  - 产品包状态为未审或已反审。
  - 客留存非空。
  - 产品包明细非空。
- **状态变更**：
  - `wh_pack.pack_status`：`INIT/INFIRM -> CONFIRM`
  - `wh_pack.approved_time`、`wh_pack.approved_man`、`wh_pack.pack_date` 被更新
- **异常情况**：
  - 产品包不存在。
  - 状态不是未审或已反审。
  - 客留存为空。
  - 明细为空。
  - 单件包小包不存在。
  - 复核毛重校验不通过。

### saveAndConfirm
> `PackController.java#saveAndConfirm()` `→` `PackBusiness.java#saveAndConfirm()` `→ L169 / L1266`

- **入口类型**：HTTP 接口
- **用途**：若已有 `id` 则先更新后审核；否则先创建后审核。
- **入参**：
  - `PackUpdateReqVO` - 请求对象
- **出参**：
  - `Long` - 产品包 ID
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.saveAndConfirm()`。
- **业务逻辑**：
  1. `id` 非空时调用 `update()`。
  2. `id` 为空时将请求转换后调用 `create()`，并回写生成的 `id`。
  3. 调用 `confirm(id)` 完成审核。
- **前置条件**：
  - 更新分支需满足 `update()` 的前置条件。
  - 新建分支需满足 `create()` 的前置条件。
- **状态变更**：
  - 新建分支会新增主单和明细。
  - 最终会把产品包状态推进到 `CONFIRM`。
- **异常情况**：
  - 继承 `create()`、`update()`、`confirm()` 的所有异常情况。

### infirm
> `PackController.java#infirm()` `→` `PackBusiness.java#infirm()` `→ L177 / L1279`

- **入口类型**：HTTP 接口
- **用途**：反审已审核的产品包。
- **入参**：
  - `id` - `Long` - 产品包 ID
- **出参**：
  - `Boolean` - 是否成功
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.infirm()`。
- **业务逻辑**：
  1. 校验产品包存在。
  2. 校验 `transferStatus` 不处于调拨中状态。
  3. 校验当前状态必须是 `CONFIRM`。
  4. 若审核日期跨天，则校验当前登录人具备跨日反审权限。
  5. 校验 `wareId == currWareId`。
  6. 若存在“可审核的关联调拨单”，则逐个作废。
  7. 更新 `wh_pack.pack_status = INFIRM`。
  8. 若关联维修单，则回退维修单已打包数量。
- **前置条件**：
  - 产品包状态必须是已审。
  - 产品包当前所在仓库必须等于单据仓库。
- **状态变更**：
  - `wh_pack.pack_status`：`CONFIRM -> INFIRM`
  - `updateForStatus()` 内会清空 `approvedMan`、`approvedTime`
- **异常情况**：
  - 调拨状态不允许反审。
  - 状态不是已审。
  - 无跨日反审权限。
  - 产品包不在当前仓库。

### obsolete
> `PackController.java#obsolete()` `→` `PackBusiness.java#discard()` `→ L185 / L1318`

- **入口类型**：HTTP 接口
- **用途**：作废未审或已反审的产品包。
- **入参**：
  - `id` - `Long` - 产品包 ID
- **出参**：
  - `Boolean` - 是否成功
- **业务逻辑**：
  1. 校验产品包存在。
  2. 校验调拨状态不处于调拨中。
  3. 校验产品包状态只能是 `INIT` 或 `INFIRM`。
  4. 更新 `wh_pack.pack_status = DISCARD`。
  5. 调用 `customerRetainService.packingBack(packNo)` 回退客留存。
  6. 调用 `customerOrderService.untieItemByPack(packId)` 解绑订单明细，并重算客户订单状态。
- **状态变更**：
  - `wh_pack.pack_status`：`INIT/INFIRM -> DISCARD`
- **异常情况**：
  - 调拨状态不允许作废。
  - 状态不是未审或已反审。

### backRejected
> `PackController.java#backRejected()` `→` `PackBusiness.java#backRejected()` `→ L565 / L1560`

- **入口类型**：HTTP 接口
- **用途**：对产品包关联调拨单执行退回拒签。
- **入参**：
  - `id` - `Long` - 产品包 ID
- **出参**：
  - `Boolean` - 是否成功
- **业务逻辑**：
  1. 校验产品包存在。
  2. 读取 `transferBillId`。
  3. 单件包调用 `transferBillService.backRejected(transferBillId)`。
  4. 成品包调用 `goodsTransferBillService.backRejected(transferBillId)`。
- **回填说明**：
  - 两类调拨单都要求当前调拨单状态为 `RETURNING`。
  - 单件包场景：`wh_transfer_bill.bill_status` 从 `RETURNING` 变为 `BACK_REJECTED`，并把源产品包 `transferStatus` 回写为 `SIGNED`。
  - 成品包场景：`wh_goods_transfer_bill.bill_status` 从 `RETURNING` 变为 `BACK_REJECTED`，并把源产品包 `transferStatus` 回写为 `SIGNED`。

### backReceived
> `PackController.java#backReceived()` `→` `PackBusiness.java#backReceived()` `→ L573 / L1573`

- **入口类型**：HTTP 接口
- **用途**：对产品包关联调拨单执行退回接收。
- **入参**：
  - `id` - `Long` - 产品包 ID
- **出参**：
  - `Boolean` - 是否成功
- **业务逻辑**：
  1. 校验产品包存在。
  2. 读取 `transferBillId`。
  3. 单件包调用 `transferBillService.backReceived(transferBillId)`。
  4. 成品包分支未执行任何处理，直接返回成功。（成品包退回接收的业务不走这个接口）

### generatePack
> `PackController.java#generatePack()` `→` `PackBusiness.java#generatePack()` `→ L581 / L1588`

- **入口类型**：HTTP 接口
- **用途**：按当前请求直接生成产品包，可选立即审核。
- **入参**：
  - `PackCreateReqVO` - 请求对象
  - `confirm` - `Boolean` - 是否创建后立即审核
- **出参**：
  - `Long` - 产品包 ID
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`，见 `PackBusiness.generatePack()`。
- **业务逻辑**：
  1. 校验字印、客户、业务员、业务分区。
  2. 按 `bizType` 分流校验单件客留存或成品客留存。
  3. 若 `packNo` 为空则生成单号。
  4. 校验仓库存在，且 `warehouse.shopId == reqVO.shopId`。
  5. 生成主单并写入 `wh_pack`。
  6. 初始化客留存、写入 `wh_pack_detail`，单件包写入 `wh_tray`。
  7. 更新客留存、订单明细和订单状态。
  8. 若 `confirm == true`，继续调用 `confirm(packId)`。
- **前置条件**：
  - 客户、业务员、业务分区存在。
  - 仓库存在且仓库归属网点与请求网点一致。

### genConfirmTransfer
> `PackController.java#genConfirmTransfer()` `→` `PackBusiness.java#genConfirmTransfer()` `→ L590 / L2108`

- **入口类型**：HTTP 接口
- **用途**：根据产品包生成调拨单，并立即审核调拨单。
- **入参**：
  - `PackRespVO` - 产品包展示对象 - 至少使用 `id`、`saleWareId`、`bizType`
  - `transferType` - `String` - 调拨类型
- **出参**：
  - `Boolean` - 是否成功
- **业务逻辑**：
  1. 校验 `saleWareId` 不为空。
  2. 校验产品包存在。
  3. 校验结算仓存在。
  4. 基于产品包和 `transferType` 生成调拨明细。
  5. 单件包生成 `TransferBill` 并调用 `confirm()`。
  6. 成品包生成 `GoodsTransferBill` 并调用 `comfirm()`。
- **前置条件**：
  - 必须指定结算仓。
- **回填说明**：
  - 单件包场景：`transferBillService.confirm()` 会调用源单回写逻辑，将产品包写成：
    - `transferBillId = {调拨单ID}`
    - `transferBillNo = {调拨单单号}`
    - `transferType = {本次transferType}`
    - `transferStatus = BE_TRANSFERRED`
  - 成品包场景：`goodsTransferBillService.comfirm()` 也会写回同一组字段，并把 `transferStatus = BE_TRANSFERRED`。

### preCheck
> `PackController.java#preCheck()` `→` `PackBusiness.java#preCheck()` `→ L600 / L2133`

- **入口类型**：HTTP 接口
- **用途**：在正式生成产品包前，检查当前提交的明细与客留存是否一致，并返回剩余未匹配客留存。
- **入参**：
  - `PackPreCheckReqVO` - 请求对象 - 包含 `retainIds`、`detailList`、`goodsList`
- **出参**：
  - `PackPreCheckRespVO` - 预检查结果
- **事务边界**：Controller 标注 `@Transactional(rollbackFor = Exception.class)`。
- **业务逻辑**：
  1. 若 `retainIds` 为空，直接返回成功结果。
  2. 读取客留存和客留存明细，按 `retainRecordId` 建立索引。
  3. 对单件明细：只要 `retainRecordId` 存在于客留存明细中，就从待匹配集合中移除；否则清空该 `retainRecordId`。
  4. 对成品明细：除 `retainRecordId` 存在外，还要求 `epc`、`skuNo`、`num`、`goldWeight` 与客留存记录相等，否则清空该 `retainRecordId`。
  5. 若仍有未匹配客留存明细，则返回 `checkRes = false`，并附带剩余待处理客留存列表。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|

## 当前结论
- 产品包模块的主状态流至少包含：`INIT`、`CONFIRM`、`INFIRM`、`DISCARD`、`RECEIVED`。
- `create/update/generatePack` 都会联动客留存与客户订单模块。
- 产品包回写客户订单时，最终订单状态取决于 `wh_customer_order_item.local_status` 的聚合结果，而不是仅由产品包状态直接决定。
- 产品包发起单件或成品调拨审核后，都会把 `transferBillId/transferBillNo/transferType/transferStatus` 回写到产品包主表。
- `confirm/infirm` 还会联动展销明细、维修单、同步任务和调拨单。
