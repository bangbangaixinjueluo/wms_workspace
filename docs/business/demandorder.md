# 订单需求单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责订单需求单的创建、修改、审核、反审、作废、批量接单、批量驳回和分店修改，并承接客户订单转需求单后的状态衔接。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_demand_order | 读写 | 订单需求单主表，对应 `DemandOrderDO` |
| wh_demand_order_detail | 读写 | 订单需求单明细表，对应 `DemandOrderDetailDO` |

## 接口列表

### createDemandOrder
> `DemandOrderController.java#createDemandOrder()` `→` `DemandOrderServiceImpl.java#createDemandOrder()` `→ L82 / L149`

- **用途**：创建订单需求单。
- **前置条件**：
  - `bizType` 必填
  - 客户型需求单要求 `customerId` 非空
  - 柜台型需求单要求 `wareId` 非空
- **业务逻辑**：
  1. 按 `bizType` 分流：
     - 客户型：`demandOrderType = CUSTOMER`，生成客户需求单号
     - 柜台型：`demandOrderType = COUNTER`，生成普通需求单号
  2. 转换为 `DemandOrderDO`，写入创建人和更新人。
  3. 对明细执行 `handlerDetail()`：
     - 校验 `skuNo` 非空
     - 校验 SKU 存在且启用
     - 汇总总件数和预估总克重
     - 修正/校验行号
  4. 回填客户信息和预计交期。
  5. 写入主表和明细表。
  6. 新建后调用 `doCustomerOrderStatus(demandOrder, false)` 处理关联客户订单状态。

### updateDemandOrder
> `DemandOrderController.java#updateDemandOrder()` `→` `DemandOrderServiceImpl.java#updateDemandOrder()` `→ L90 / L319`

- **用途**：更新订单需求单。
- **前置条件**：仅允许 `DemandOrderStatusEnum.canEdit()` 的状态。
- **业务逻辑**：
  1. 校验需求单存在且状态允许编辑。
  2. 重新回填客户信息。
  3. 重新执行 `handlerDetail()` 汇总与校验。
  4. 更新主表。
  5. 删除旧明细并重建新明细。

### confirm
> `DemandOrderController.java#confirm()` `→` `DemandOrderServiceImpl.java#confirm()` `→ L332 / L542`

- **用途**：审核需求单。
- **前置条件**：
  - 当前状态必须是 `INIT` 或 `INFIRM`
  - `requireDate` 不能为空
  - 明细不能为空
- **状态变更**：
  - `wh_demand_order.order_status`：`INIT/INFIRM -> CONFIRM`
  - 写入 `approvedMan`、`approvedTime`
  - 明细写入 `stockNumSnapshot`
- **业务逻辑**：
  1. 校验状态、要求交期、明细。
  2. 更新审核人、审核时间和订单状态。
  3. 计算每行库存快照并回写到明细。
  4. 发送审核通过通知。

### infirm
> `DemandOrderController.java#infirm()` `→` `DemandOrderServiceImpl.java#infirm()` `→ L341 / L596`

- **用途**：反审需求单。
- **前置条件**：状态必须满足 `DemandOrderStatusEnum.canInfirm()`。
- **状态变更**：
  - `wh_demand_order.order_status -> INFIRM`
  - 清空 `approvedMan`、`approvedTime`
  - `wh_demand_order_detail.stock_num_snapshot -> null`

### discard
> `DemandOrderController.java#discard()` `→` `DemandOrderServiceImpl.java#discard()` `→ L350 / L618`

- **用途**：作废需求单。
- **前置条件**：通过 `BillStatusEnum.obsoleteCheck(orderStatus)` 校验当前状态允许作废。
- **状态变更**：
  - `wh_demand_order.order_status -> DISCARD`
  - `wh_demand_order.return_status -> CLOSED`
- **业务逻辑**：
  1. 更新需求单状态为作废。
  2. 调用 `doCustomerOrderStatus(orderDO, true)` 回退关联客户订单状态。

### taking
> `DemandOrderController.java#taking()` `→` `DemandOrderServiceImpl.java#batchTaking()` `→ L419 / L702`

- **用途**：批量接单。
- **前置条件**：所有单据当前状态必须是 `CONFIRM`。
- **状态变更**：
  - `wh_demand_order.order_status`：`CONFIRM -> RECEIVED`
  - `wh_demand_order.return_status`：`* -> IN_PRODUCTION`

### reject
> `DemandOrderController.java#reject()` `→` `DemandOrderServiceImpl.java#batchReject()` `→ L427 / L723`

- **用途**：批量驳回需求单。
- **前置条件**：状态必须满足 `DemandOrderStatusEnum.canReject()`，即待接单或已接单。
- **状态变更**：
  - `wh_demand_order.order_status -> REJECTED`
  - `wh_demand_order.return_status -> TO_BE_OUTSOURCED`
  - `wh_demand_order.reject_reason = {reason}`

### modifyBranch
> `DemandOrderController.java#modifyBranch()` `→` `DemandOrderServiceImpl.java#modifyBranch()` `→ L466 / L1068`

- **用途**：批量修改需求单明细的分店字段。
- **前置条件**：仅允许 `DemandOrderStatusEnum.canEdit()` 的状态。
- **状态变更**：
  - `wh_demand_order_detail.branch_store = {branch}`

## 公共规则

### 客户订单转需求单
> `CustomerOrderReceiveBusiness.java#toDemandOrder()` `→ L255-L280`

- 若客户订单当前状态已经是 `WAIT_COMEBACK`，则直接返回已存在的“普通需求单”ID，不再重复推进状态。
- 若客户订单当前状态是 `WAIT_PICKING`，则允许转需求单，并把客户订单状态更新为：
  - `wh_customer_order.order_status`：`WAIT_PICKING -> WAIT_COMEBACK`
- 转换后会发送订单通知，`tellerSettleStatus` 写为 `IN_PRODUCTION`。
- 需求单侧通过 `getNormalDemandOrderIdByCustomerOrderNo()` 查询同一客户订单号下、状态为 `INIT/INFIRM` 的普通需求单。

## 当前结论
- 需求单模块本身维护的是“需求单主表 + 明细 + 库存快照 + 接单/驳回状态”，但它同时承担客户订单进入“待回货”阶段的桥接作用。
- `confirm -> taking/reject -> infirm/discard` 构成了需求单主状态流，其中 `taking` 和 `reject` 决定需求单是进入生产还是退回待委外处理。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
