# 客户订单-意向单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-30

## 模块职责
负责意向单场景下的现场挑货、柜台拣货、拣货完成、清空拣货中和作废，并在拣货完成时生成客留存，必要时同步生成产品包。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| wh_customer_order | 读写 | 客户订单主表，对应 `CustomerOrderDO` |
| wh_customer_order_detail | 读写 | 客户订单款式明细表 |
| wh_customer_order_item | 读写 | 客户订单明细表，本地状态驱动订单状态变化 |
| wh_customer_retain | 读写 | 拣货完成后生成客留存主表 |
| wh_customer_retain_record | 读写 | 拣货完成后生成客留存明细 |
| wh_pack | 读写 | 勾选 `genPack=true` 时同步生成产品包 |

## 接口列表

### siteChoose
> `CustomerOrderIntentController.java#siteChoose()` `→` `CustomerOrderIntentBusiness.java#siteChoose()` `→ L66 / L184`

- **用途**：现场挑货创建意向单，并锁定对应单件。
- **业务逻辑**：
  1. 生成订单号。
  2. 转换为 `CustomerOrderDO` 并回填客户、业务员、业务分区信息。
  3. 读取 `itemDetails` 对应的 SKU 和 EPC。
  4. 基于单件明细反推订单款式明细。
  5. 校验成品库存。
  6. 汇总订单总件数、总金重、已拣件数、已拣金重。
  7. 写入订单主表。
  8. 对每个有效 EPC 执行客户锁定，并写单件日志。
  9. 新建 `wh_customer_order_item`，本地状态统一写为 `PICKING`。
  10. 新建 `wh_customer_order_detail`。
- **状态变更**：
  - 新建订单明细时，`wh_customer_order_item.local_status = PICKING`

### tellerPick
> `CustomerOrderIntentController.java#tellerPick()` `→` `CustomerOrderIntentBusiness.java#tellerPick()` `→ L87 / L451`

- **用途**：柜台拣货。
- **业务逻辑**：
  1. 先读取订单。
  2. 按订单业务类型分流：
     - `SO_FIELD_SPOT` 走 `handleChoose()`
     - 其他走 `handlePick()`
  3. `handlePick()` 中会校验订单状态允许拣货。
  4. 重新汇总订单件数和金重。
  5. 校验成品库存。
  6. 校验新增拣货明细必须能匹配到订单原款式，且累计拣货数量不能超过需求数量。
  7. 对新增 EPC 执行客户锁定，新增日志。
  8. 对移除的旧 EPC 执行解锁并删掉对应订单明细。
  9. 新增或保留 `wh_customer_order_item`，新增项状态写为 `PICKING`。

### pickCompleted
> `CustomerOrderIntentController.java#pickCompleted()` `→` `CustomerOrderIntentBusiness.java#pickCompleted()` `→ L101 / L867`

- **用途**：完成指定拣货仓的拣货，并生成客留存；可选同步生成产品包。
- **前置条件**：
  - 当前登录人必须有 `pickWareId` 仓权限
  - 当前仓下必须存在 `localStatus = PICKING` 的订单明细
- **业务逻辑**：
  1. 读取订单、客户和订单明细。
  2. 过滤出当前 `pickWareId` 下、本地状态为 `PICKING` 的明细。
  3. 按“是否有 EPC”分组，分别生成单件客留存或成品客留存。
  4. 创建 `CustomerRetainDO`，汇总件数和金重。
  5. 将本批订单明细更新为 `RETAIN`。
  6. 若 `genPack = true`，则进一步生成产品包，并把本批订单明细改为：
     - `packId = {新产品包ID}`
     - `localStatus = BILLING`
  7. 保存客留存主表和客留存明细。
  8. 最后调用 `customerOrderStatusChange(orderId)` 重算订单状态。
- **状态变更**：
  - 默认：`wh_customer_order_item.local_status`：`PICKING -> RETAIN`
  - 生成产品包时：`wh_customer_order_item.local_status`：`PICKING -> BILLING`
  - 生成产品包时：`wh_customer_order_item.pack_id = {packId}`

### discard
> `CustomerOrderIntentController.java#discard()` `→` `CustomerOrderIntentBusiness.java#discard()` `→ L165 / L1379`

- **用途**：作废现场挑货类意向单。
- **前置条件**：
  - 订单业务类型必须是 `SO_FIELD_SPOT`
  - 订单状态必须是 `WAIT_PICKING`
- **业务逻辑**：
  1. 把订单状态更新为 `DISCARD`。
  2. 读取订单下所有 EPC。
  3. 执行 EPC 解锁并写解锁日志。
  4. 删除订单明细项。
- **状态变更**：
  - `wh_customer_order.order_status`：`WAIT_PICKING -> DISCARD`

### clearPick
> `CustomerOrderIntentController.java#clearPick()` `→` `CustomerOrderIntentBusiness.java#clearPick()` `→ L182 / L1437`

- **用途**：清空“拣货中”的订单明细。
- **业务逻辑**：
  1. 读取订单和订单明细。
  2. 过滤出当前登录人有权限仓库下、且 `localStatus = PICKING` 的明细。
  3. 若传了 `pickWareId`，则进一步按仓过滤。
  4. 对这些明细关联的 EPC 执行解锁。
  5. 写解锁日志。
  6. 删除被清理的订单明细。
- **状态变更**：
  - 被清理的 `wh_customer_order_item` 直接删除，不再保留 `PICKING` 状态。

## 公共规则

### 意向单拣货完成后的订单状态
- `pickCompleted()` 末尾调用 `customerOrderService.customerOrderStatusChange(orderId)`。
- 因此，最终订单状态不由意向单接口直接硬编码，而由订单明细本地状态聚合决定：
  - 仍有 `RETAIN` 时，订单进入 `WAIT_SETTLE`
  - 若本批直接生成产品包导致存在 `BILLING`，订单进入 `SETTLING`

## 当前结论
- 意向单模块的核心不是单纯修改订单，而是维护“订单明细本地状态 + EPC 锁定 + 客留存/产品包生成”的联动链路。
- `pickCompleted()` 是当前最关键的业务转换点：它把拣货中的订单明细推进为客留存，或者直接推进到产品包开单链路。
