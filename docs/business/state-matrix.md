# 状态矩阵总表
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 1. 文档定位

本表用于回答 4 个高频问题：
- 一个模块到底有几套状态在并行运转
- 哪些状态是人工按钮驱动，哪些状态是聚合重算
- 单头状态和明细状态分别由谁维护
- 改某个模块时，应该顺带回归哪些联动状态

阅读建议：
- 先看 [overview.md](/D:/ws/code/wms-api/docs/business/overview.md) 的业务地图
- 再用本表定位状态口径
- 最后回到模块正文和状态图核对细节

## 2. 状态分类说明

| 类型 | 含义 | 常见来源 |
|------|------|----------|
| 单头主状态 | 单据主表的业务阶段 | 审核、反审、作废、接单、完工 |
| 明细/局部状态 | 明细行、单件、库位、EPC 的局部状态 | 拣货、留货、字印、到柜、锁定 |
| 派生状态 | 由累计量或明细状态聚合出来的状态 | 已完结、部分到柜、部分委外、已发货 |
| 外围状态 | 外部同步、平台回调、流程审批状态 | syncStatus、outOrderStatus、processResult |

## 3. 核心交易链状态矩阵

| 业务域 | 模块 | 单头主状态 | 明细/局部状态 | 派生/并行状态 | 主维护来源 | 改造时重点 |
|------|------|------|------|------|------|------|
| 客户履约 | 客户订单 | `UN_SUBMIT/WAIT_TAKING/WAIT_PICKING/WAIT_COMEBACK/WAIT_SETTLE/SETTLING/SETTLED/DELIVERED/DISCARD` | `wh_customer_order_item.localStatus`：`PICKING/RETAIN/BILLING/SETTLEMENT/SEAL/BILLED/SHIPPED` | `outOrderStatus/syncStatus/staffStatus/tellerSettleStatus` 并行 | 订单服务 + 意向单业务层 + 出货回调 | 不能只改 `orderStatus`，必须联动局部状态和外部状态 |
| 客户履约 | 客留存 | 留存单头状态 | 留存明细、单件锁定 | 与客户订单、产品包联动 | 客留存业务层 | 留存不是孤立状态，强依赖订单与单件锁 |
| 销售执行 | 产品包 | 包单状态 | 包明细 / 小包 / 字印挂接 | 调拨状态、开单状态并行 | PackBusiness | 包单常是上游宿主，很多状态来自下游回写 |
| 调拨执行 | 调拨单 | 审核、接收、退回、作废 | 调拨明细 / 单件实际流向 | 目的仓接收态 | TransferBill / GoodsTransferBill | 接收态和审核态不是一回事 |
| 需求承接 | 需求单 | `INIT/CONFIRM/INFIRM/DISCARD/RECEIVED/REJECTED/PART_OUT/ALL_OUT/COMPLETED/CANCELED` | 明细累计：`producing/returned/transfer/entry/arrived/cancel` | `returnStatus`：`TO_BE_OUTSOURCED/IN_PRODUCTION/IN_QC/COMPILING/ON_THE_WAY/PART_ARRIVED/ARRIVED_COUNTER/CLOSED` | 需求单聚合 Mapper 按明细重算 | 这是典型“单头状态派生模块”，新增按钮要先确认是否会被重算覆盖 |
| 委外加工 | 委外加工订单 | `INIT/CONFIRM/INFIRM/DISCARD/CANCELLATION/DELIVERED/FINISH` | 委外明细累计核销、回货、入库挂接 | 需求单委外累计反向回写 | 委外单服务 + 核销服务 | 委外主状态很多不是人工直接设置，而是由核销累计反推 |
| 核销链 | 订单核销 | 审核、反审、作废 | 核销明细事实层 | 委外单状态、需求单累计同步变化 | OrderWriteoff + 明细聚合 | 它主要改委外和需求，不直接改销售单头 |
| 销售结算 | 出库结算单 | 结算、发货申请、发货、退回、作废 | 结算明细、包单挂接、客户订单挂接 | 出货同步状态 | SaleBillBusiness + DeliveryBusiness | 既有内部状态，也有外部履约状态 |
| 履约出口 | 出货单 | `待出货/出货中/已出货/失败/作废` | 出货明细、同步流水 | 老庙回调、共享库存异步链 | DeliveryBusiness | 出货成功不等于所有异步链结束 |

## 4. 库存与单件链状态矩阵

| 业务域 | 模块 | 单头主状态 | 明细/局部状态 | 派生/并行状态 | 主维护来源 | 改造时重点 |
|------|------|------|------|------|------|------|
| 单件执行 | 单件入库单 | 建单、审核、反审、作废 | 新建 `wh_item`、入库明细、辅石快照 | 需求单累计、维修来源回写 | EntryBillService / EntryBillBusiness | 审核会同时改单件、库存、需求、维修四条链 |
| 单件执行 | 单件调整单 | 提审、审批通过、反审、作废 | 单件本体字段、库存旧出新入 | 维修新件信息回写 | AdjustBill | 局部状态主要在单件和库存，不在单头 |
| 单件执行 | 单件作废单 | 提审、审批通过、反审、作废 | 单件状态 `DISCARD`、库存出入 | 成品库存补回 | CancelBill | “作废”不是删除，是状态迁移 |
| 盘点分析 | 单件盘点单 | 审核、反审、作废 | 批次、盘点快照、盘盈池 | 差异统计页 | ItemInventoryBill | 单头状态和正式库存过账目前是弱耦合 |
| 库位执行 | 库位调整单 | 审核、反审、作废 | EPC 锁定、单件库位、库位库存分布 | 外部同步侧链 | ShelvesBill | 局部状态在 EPC 锁和库位库存，不只是单头 |
| 单件域 | 单件明细 | 无独立审批单头 | `wh_item` 状态、锁状态、客锁、维修态 | `wh_item_log` 事件流 | ItemService | 它是多条执行链的共同落点 |
| 盘点分析 | 盘点称重单 | 审核、反审、作废 | 称重明细、库存基线快照 | 盈亏差值分析结果 | InventoryWeighingBill | 主要生成分析结果，不直接等于库存执行 |
| 库存主表 | SKU/成品/原料库存 | 无统一审批单头 | 实时库存、日报、历史快照 | 差异、告警、共享库存口径 | 各库存服务 | 绝大多数状态来自业务单据反向写入 |

## 5. 资金与加工链状态矩阵

| 业务域 | 模块 | 单头主状态 | 明细/局部状态 | 派生/并行状态 | 主维护来源 | 改造时重点 |
|------|------|------|------|------|------|------|
| 资金链 | 收款单 | 审核、反审、作废、分账/取消分账 | 收款明细、分账子表 | 银行余额 + 钱包余额双账本 | ReceivableBill | 分账只重分配钱包，不重做银行入账 |
| 资金链 | 付款单 | 审核、反审、作废 | 付款明细 | 银行扣减 + 钱包增加双账本 | PayableBill | 是收款单的镜像链 |
| 资金链 | 应收/应付调整、调账单、结价 | 审核、反审、作废 | 调整明细、结算明细 | 现金钱包、材质钱包、欠料欠款并行 | 各财务服务 | 需要先分清“金额链”和“金重链” |
| 来料出料 | 客户/供应商来料出料 | 审核、反审、作废 | 来料/出料明细 | 原料库存 + 钱包 + 结价冲回并行 | MaterialInbound/Outbound | 这类模块通常同时改库存和钱包 |
| 退厂转料 | 成品退厂、登记、转料 | 审核、反审、作废 | 登记池、来源挂接、单件转料 | 半成品回流、维修占用、供应商钱包 | GoodsReject / GoodsRecycle | 不能只看库存，很多状态在来源池和挂接链 |
| 维修域 | 维修单、维修入库、维修出库、维修详情 | 审核、反审、终结、作废 | 明细累计、单件维修态、退厂登记池、供应商钱包 | `replyStatus`、`billStatus`、来源累计并行 | Maintain* 多服务协同 | 维修是多单据宿主链，最怕只改单头不改累计 |

## 6. 使用建议

- 先区分“人工状态”和“聚合状态”。
  - 例如需求单 `COMPLETED` 更像聚合结果，不是天然适合直接手工改的状态。
- 先定位“真正的主维护来源”。
  - 很多状态不在 controller/service 主流程里，而是在 mapper 聚合或下游业务层回写。
- 涉及列表按钮改造时，先确认前端按钮条件是不是直接等于后端状态机条件。
  - 这套系统里很多“页面可点”并不等于“服务端必然允许”。
- 涉及新增终态时，先确认后续聚合是否会覆盖它。
  - 这是需求单、客户订单、维修单最常见的风险。

## 7. 关联文档

- [overview.md](/D:/ws/code/wms-api/docs/business/overview.md)
- [cross-module-writeback.md](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)
- [field-semantics.md](/D:/ws/code/wms-api/docs/business/field-semantics.md)
- [change-impact.md](/D:/ws/code/wms-api/docs/business/change-impact.md)
