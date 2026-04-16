# 高频业务口径总表
> 目标：把后续最容易改错、最容易“名字像一样但业务意义不同”的高频业务口径集中收口。  
> 使用方式：先看“适用模块”，再看“计算/判断口径”，最后回到对应模块正文确认字段来源与状态前提。

## 1. 订单与委外口径

| 口径 | 适用模块 | 计算/判断口径 | 说明 | 依据文档 |
|------|----------|---------------|------|----------|
| 未到柜件数 | 需求单 | `demandNum - cancelNum - arrivedNum` | 页面展示和“是否到柜完成”判断都应先看这组累计字段 | [demandorder.md](./demandorder.md) |
| 未到柜克重 | 需求单 | `未到柜件数 × 平均克重` | 当前是派生展示口径，不是独立维护字段 | [demandorder.md](./demandorder.md) |
| 已委外 | 需求单 / 委外单 | 需求明细 `producingNum > 0` 且未全量覆盖需求 | 需求单单头状态来自聚合，不是手工逐单改写 | [demandorder.md](./demandorder.md)、[outsourcingorder.md](./outsourcingorder.md) |
| 部分委外 | 需求单 | 仅部分明细或部分数量已进入委外累计 | 页面和按钮判断不能只看单头文案，要回到明细累计字段 | [demandorder.md](./demandorder.md) |
| 全部委外 | 需求单 | 全部明细满足 `demandNum <= producingNum + cancelNum` | 对应系统里的聚合状态，不等于“人工点了全部委外” | [demandorder.md](./demandorder.md) |
| 已核销 | 委外单 / 订单核销 | 委外明细 `totalWriteoffNum` 达到委外量 | 核销吃的是合格核销量，不是总回货量 | [orderwriteoff.md](./orderwriteoff.md)、[outsourcingorder.md](./outsourcingorder.md) |
| 部分核销 | 委外单 / 订单核销 | `0 < totalWriteoffNum < outsourcingNum` | 常见中间态，需和“终止核销”区分 | [orderwriteoff.md](./orderwriteoff.md) |
| 核销完毕 | 委外单 | 全部委外明细核销完成后聚合出的终态 | 不是单头直接手工维护 | [outsourcingorder.md](./outsourcingorder.md) |
| 已到柜 | 需求单 | 全部明细满足 `demandNum <= arrivedNum + cancelNum` | 更接近“需求已收口/已完结”的业务判断 | [demandorder.md](./demandorder.md) |
| 部分到柜 | 需求单 | 已有 `arrivedNum`，但未全量覆盖需求 | 不能和“部分委外”混看 | [demandorder.md](./demandorder.md) |
| 未回货件数/克重 | 维修 / 委外 / 加工链 | 一般指“应回未回”的差值，但各模块口径不同 | 不能跨模块复用同一公式，必须回各自正文核对 | [maintainbill.md](./maintainbill.md)、[outsourcingorder.md](./outsourcingorder.md) |

## 2. 资金与钱包口径

| 口径 | 适用模块 | 计算/判断口径 | 说明 | 依据文档 |
|------|----------|---------------|------|----------|
| 钱包金额余额 | 钱包 / 收付款 / 调账 / 来料出料 / 维修 | `wh_wallet.balance` | 现金钱包主账，很多模块审核/反审都直接改它 | [wallet.md](./wallet.md)、[receivablebill.md](./receivablebill.md)、[payablebill.md](./payablebill.md) |
| 钱包金重余额 | 钱包 / 来料出料 / 维修 / 调整 | 材质钱包下的重量余额 | 一般和 `materialId + walletTypeNo + settleClass` 组合出现 | [wallet.md](./wallet.md)、[materialinbound.md](./materialinbound.md)、[materialoutbound.md](./materialoutbound.md) |
| 欠款 | 钱包 / 应收应付调整 / 结价 | 债务口径的现金负债 | 不能直接等同余额正负，需结合 `accountingType` 看 | [customeradjustbill.md](./customeradjustbill.md)、[supplieradjustbill.md](./supplieradjustbill.md)、[closingprice.md](./closingprice.md) |
| 欠料 | 钱包 / 来料出料 / 结价 | 债务口径的材质负债 | 常见于材质钱包和来料链 | [materialinbound.md](./materialinbound.md)、[suppliermaterialinbound.md](./suppliermaterialinbound.md) |
| 银行卡余额 | 收款 / 付款 / 内部转账 | `wh_shop_bank.balance` | 属于真实银行侧底账，不等于钱包余额 | [shopbank.md](./shopbank.md)、[internalremittance.md](./internalremittance.md) |
| 分账 | 收款单 | 只重分配钱包归属，不重做银行侧入账 | 收款已入银行后，分账只是钱包归属变化 | [receivablebill.md](./receivablebill.md)、[receivabledividebill.md](./receivabledividebill.md) |
| 结价 | 销售/退货/维修/来料中的 `CLOSING_PRICE(02)` | 先并主账，再对材质钱包做反向冲回 | 不是独立单头流程，而是宿主单据中的特殊结算项 | [closingprice.md](./closingprice.md)、[settlement.md](./settlement.md) |

## 3. 库存、退厂与维修口径

| 口径 | 适用模块 | 计算/判断口径 | 说明 | 依据文档 |
|------|----------|---------------|------|----------|
| 可退厂池 | 成品退厂登记单 | 已登记、可被退厂单承接的候选池 | 本身不直接改库存和钱包，关键看 `billStatus + outOfWare` | [goodsrejectrecord.md](./goodsrejectrecord.md) |
| 已退厂/出库中 | 成品退厂登记单 / 退厂单 | 由 `outOfWare` 及来源挂接状态决定 | 不是只看单头审核态 | [goodsrejectrecord.md](./goodsrejectrecord.md)、[goodsrejectbill.md](./goodsrejectbill.md) |
| 维修终结 | 维修单详情 / 维修单 | `finalization(...)` 把明细推进到 `COMPLETED`，全部完成时带动单头完结 | “终结”不是普通审核态，而是维修生命周期收口动作 | [maintainbilldetail.md](./maintainbilldetail.md)、[maintainbill.md](./maintainbill.md) |
| 维修回厂完成 | 维修报表 / 维修明细 | 报表重算出来的“已回/未回/终结” | 不完全等于原表 `replyStatus` 原值 | [maintainbillreport.md](./maintainbillreport.md) |
| 单件库存 | 单件入库/调整/作废/盘点/库位 | 单件数量与库位分布的实时执行结果 | 很多执行链都同时改 `wh_item`、`wh_item_log` 和库存表 | [entrybill.md](./entrybill.md)、[adjustbill.md](./adjustbill.md)、[item.md](./item.md) |

## 4. 共享与外部履约口径

| 口径 | 适用模块 | 计算/判断口径 | 说明 | 依据文档 |
|------|----------|---------------|------|----------|
| 共享库存 | 新版共享库存 / 共享方 / 老庙链 | 按 `modelNo + specs` 等共享维度聚合的对外库存 | 不是本地单件库存原样透出，而是对外共享口径库存 | [sharestock.md](./sharestock.md)、[shareparty.md](./shareparty.md) |
| 旧版共享库存 | `stockshare` 旧链 | 旧版共享库存中心表与事件表 | 与新版 `share_stock` 并存，不能混用 | [stockshare.md](./stockshare.md)、[stocksharelog.md](./stocksharelog.md) |
| 出货成功 | 出货单 / 客户订单 | 出货单回调成功 + 后续共享库存异步链完成 | 不能只看出货单本身已发货，还要看后续同步侧链 | [deliverybill.md](./deliverybill.md)、[customerorder.md](./customerorder.md) |
| 同步成功 | 接口同步任务 / 老庙接口痕迹 | 任务状态 + 回调结果 + 业务回写结果三者综合判断 | 不能只看 `wh_sync_task` 单表 | [synctask.md](./synctask.md)、[lmapitransfer.md](./lmapitransfer.md) |

## 5. 使用提醒

- 同名口径不要跨模块复用，尤其是“未回货”“完成”“结价”“已同步”。
- 先分清“人工状态”“聚合状态”“报表重算状态”，再改页面按钮或流程条件。
- 涉及钱包时，至少同时确认：现金金额、材质金重、银行卡余额、日志口径。
- 涉及委外、维修、退厂时，不要只看单头状态，还要看来源挂接和累计字段。

## 6. 关联文档

- [系统模块总览](./overview.md)
- [状态矩阵总表](./state-matrix.md)
- [字段语义表](./field-semantics.md)
- [跨模块回写清单](./cross-module-writeback.md)
