# 系统模块总览
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 1. 文档定位

本文件是业务文档体系的总入口，用于：
1. 查看全量模块盘点结果
2. 查看推荐分析顺序和当前执行进度
3. 快速进入模块文档、图表和总表类产物
4. 集中跟踪全局未关闭标记

建议阅读顺序：
1. [analysis-workflow.md](/D:/ws/code/wms-api/docs/business/analysis-workflow.md)
2. [session-handoff.md](/D:/ws/code/wms-api/docs/business/session-handoff.md)
3. 当前文件
4. 目标模块文档及相关图表/总表

### 1.1 系统业务地图
- 客户需求入口层：客户、客户款式、客户订单、意向单、客留存，负责把“谁要什么货”转成系统内的业务请求。
- 执行中台层：产品包、调拨、成品调拨、需求单、单件入库/调整/作废、库位调整、盘点，负责把订单请求转成具体货、具体库存和具体库位动作。
- 加工生产层：来料、出料、退货、退厂、转料、维修、委外，负责承接非直接销售场景下的加工、返修、提纯和外发链。
- 资金结算层：收款、付款、调账、应收应付调整、结价、钱包、银行卡，负责把业务动作落到账户钱包和网点银行卡两套底账。
- 履约出口层：出库结算、出货、老庙接口、同步任务、共享库存，负责把内部执行结果推进到外部履约和平台回调。
- 主数据规则层：客户、供应商、仓库、材质、大类、品类、产品、SKU、BOM、工艺、设计元素、金价、单价、结算方式，负责给上面五层提供统一口径。

### 1.2 首页使用方式
- 想快速理解系统：先读本节业务地图，再读 [state-matrix.md](/D:/ws/code/wms-api/docs/business/state-matrix.md)。
- 想定位模块：从 2 节模块清单进入，再回到 7 节查看配套图。
- 想排查联动：优先看 [cross-module-writeback.md](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)。
- 想确认字段口径：优先看 [field-semantics.md](/D:/ws/code/wms-api/docs/business/field-semantics.md)。
- 想看遗留风险：直接看 8 节集中待办。

## 2. 模块清单

说明：
- `✅ 已完成` 表示模块正文和基础配套图已落盘。
- `🟡 已完成首轮` 表示主文档已落盘，但仍可能存在未关闭风险或后续补充空间。
- `⏳ 未覆盖` 表示当前尚未单独做业务正文。
- 本节已把“已有正文”的模块统一收敛为 `✅ 已完成`；未关闭风险统一看 8 节，不再用 `🔄 进行中` 表示。

| 模块 | 职责 | 文档 | 分析状态 |
|------|------|------|----------|
| 部门 | 组织架构维护 |  | ⏳ 待分析 |
| 材质档案 | 材质基础资料维护 | [texture.md](./texture.md) | ✅ 已完成 |
| 菜单 | 菜单权限配置 |  | ⏳ 待分析 |
| 参数配置 | 系统参数维护 |  | ⏳ 待分析 |
| 仓库档案 | 仓库基础资料维护 | [warehouse.md](./warehouse.md) | ✅ 已完成 |
| 产品包 | 创建、修改、审核、反审、作废产品包，并联动客留存、订单、调拨单 | [pack.md](./pack.md) | ✅ 已完成 |
| 成品调拨单 | 成品产品包/成品明细调拨、审核、接收、退回 | [goodstransferbill.md](./goodstransferbill.md) | ✅ 已完成 |
| 成品入库单 | 成品入库、审核、反审、作废，并联动成品库存、供应商钱包、半成品回流与委外挂接 | [goodsinboundbill.md](./goodsinboundbill.md) | ✅ 已完成 |
| 成品退厂单 | 成品退厂、审核、反审、作废，并联动成品库存、供应商钱包、登记池/半成品回流挂接 | [goodsrejectbill.md](./goodsrejectbill.md) | ✅ 已完成 |
| 成品退厂登记单 | 成品退厂登记、分厂、审核、反审、作废，并维护退厂候选池与维修明细占用 | [goodsrejectrecord.md](./goodsrejectrecord.md) | ✅ 已完成 |
| 成品转料单 | 成品转料、审核、反审，并联动成品库存、原料库存与单件转料状态流 | [goodsrecyclebill.md](./goodsrecyclebill.md) | ✅ 已完成 |
| 出货单 | 发货与重试发货 | [deliverybill.md](./deliverybill.md) | ✅ 已完成 |
| 出库结算单 | 产品包接收、结算、发货申请、实际发货、退回 | [salebill.md](./salebill.md) | ✅ 已完成 |
| 打印任务单 | 打印任务创建、审核、反审、重置打印状态 | [printtask.md](./printtask.md) | ✅ 已完成 |
| 大类档案 | 大类基础资料维护 | [bigclass.md](./bigclass.md) | ✅ 已完成 |
| 单价信息维护 | 单价信息维护 | [unitprice.md](./unitprice.md) | ✅ 已完成 |
| 单件调整单 | 单件调整、预检查、提交审批、审批通过回写单件/库存/维修链、作废 | [adjustbill.md](./adjustbill.md) | ✅ 已完成 |
| 单件明细 | 单件主档查询、导出、财务视图、标签复核、客锁释放，并作为多条单件执行链共同落点 | [item.md](./item.md) | ✅ 已完成 |
| 单件盘点单操作 | 单件盘点建单、批次采集、审核/反审、作废，并维护盘点快照、盘盈数据和差异查询口径 | [iteminventorybill.md](./iteminventorybill.md) | ✅ 已完成 |
| 单件入库单 | 单件入库、预检查、审核、反审、作废，并联动单件建档、单件库存、成品库存、需求单与维修单回写 | [entrybill.md](./entrybill.md) | ✅ 已完成 |
| 单件作废单 | 单件作废、提交审批、审批通过回写单件状态与库存、反审、作废 | [cancelbill.md](./cancelbill.md) | ✅ 已完成 |
| 登录设备表 | 登录设备记录维护 |  | ⏳ 待分析 |
| 调拨单 | 单件调拨、审核、接收、退回、反审、作废 | [transferbill.md](./transferbill.md) | ✅ 已完成 |
| 调账单 | 账户间调账、审核、反审、作废，并联动调出/调入账户双向钱包过账 | [accountadjustbill.md](./accountadjustbill.md) | ✅ 已完成 |
| 订单核销 | 核销、反审、作废，并回写委外单与需求单累计核销量 | [orderwriteoff.md](./orderwriteoff.md) | ✅ 已完成 |
| 订单需求单 | 订单需求创建、接单、驳回、审核、反审 | [demandorder.md](./demandorder.md) | ✅ 已完成 |
| 定时任务 | 任务维护与触发 |  | ⏳ 待分析 |
| 付款单 | 付款、审核、反审、作废，并联动银行卡与钱包余额 | [payablebill.md](./payablebill.md) | ✅ 已完成 |
| 供应商出料单 | 供应商出料、确认、反确认、作废，并联动原料库存与供应商钱包 | [suppliermaterialoutbound.md](./suppliermaterialoutbound.md) | ✅ 已完成 |
| 供应商档案 | 供应商基础资料维护 | [supplier.md](./supplier.md) | ✅ 已完成 |
| 供应商来料单 | 供应商来料、审核、反审、作废，并联动原料库存与供应商钱包 | [suppliermaterialinbound.md](./suppliermaterialinbound.md) | ✅ 已完成 |
| 供应商应付账调整 | 供应商应付账调整、审核、反审、作废，并联动供应商钱包金额账与金重账 | [supplieradjustbill.md](./supplieradjustbill.md) | ✅ 已完成 |
| 角色 | 角色管理 |  | ⏳ 待分析 |
| 结价单 | 以 `CLOSING_PRICE(02)` 结算方式嵌入销售结算、退货、维修确认链，并联动材质钱包反向冲回 | [closingprice.md](./closingprice.md) | ✅ 已完成 |
| 金价配置 | 金价规则与个人金价配置 | [goldpricesetting.md](./goldpricesetting.md) | ✅ 已完成 |
| 客户出料单 | 客户出料、确认、反确认、作废，并联动原料库存与客户钱包 | [materialoutbound.md](./materialoutbound.md) | ✅ 已完成 |
| 客户档案 | 客户创建、修改、提交审核、删除 | [customer.md](./customer.md) | ✅ 已完成 |
| 客户订单 | 业务接单创建/提交订单，维护订单状态重算规则 | [customerorder.md](./customerorder.md) | ✅ 已完成 |
| 客户订单 - 意向单 | 挑货、拣货完成、清空拣货、作废 | [customerorder-intent.md](./customerorder-intent.md) | ✅ 已完成 |
| 客户款式管理 | 客户款式增删改 | [customersku.md](./customersku.md) | ✅ 已完成 |
| 客户来料单 | 客户来料、审核、反审、作废，并联动原料库存、客户材质钱包、现金钱包与结价冲回 | [materialinbound.md](./materialinbound.md) | ✅ 已完成 |
| 客户退货单 | 客户退货、审核、反审、作废，并联动单件/成品库存、客户钱包、结价冲回与维修仓位 | [returnbill.md](./returnbill.md) | ✅ 已完成 |
| 客户业务员修改单 | 客户业务员变更申请与提交审核 | [customerstaffupdate.md](./customerstaffupdate.md) | ✅ 已完成 |
| 客户应收账调整 | 客户应收账调整、审核、反审、作废，并联动客户钱包金额账与金重账 | [customeradjustbill.md](./customeradjustbill.md) | ✅ 已完成 |
| 客留存 | 创建、修改、延期、合并、拆分、迁移客留存，并联动客户订单与单件锁定 | [customerretain.md](./customerretain.md) | ✅ 已完成 |
| 库位调整单 | 库位调整创建、修改、审核、反审、作废，并联动单件库位、库位库存分布与外部同步 | [shelvesbill.md](./shelvesbill.md) | ✅ 已完成 |
| 款式库 | SKU 维护、审核、企业微信搜索 | [sku.md](./sku.md) | ✅ 已完成 |
| 款式收藏 | 款式收藏及标签维护 | [skufavorite.md](./skufavorite.md) | ✅ 已完成 |
| 款式修改单 | 款式修改审批、同步、批量修改 | [updatesku.md](./updatesku.md) | ✅ 已完成 |
| 流程任务实例 | BPM 审批通过/驳回 |  | ⏳ 待分析 |
| 盘点称重单 | 盘点称重创建、修改、审核、反审、作废，并回写盘点盈亏差值分析结果 | [inventoryweighingbill.md](./inventoryweighingbill.md) | ✅ 已完成 |
| 权限 | 用户角色/角色菜单授权 |  | ⏳ 待分析 |
| 认证 | 登录认证与跳转登录 |  | ⏳ 待分析 |
| 设计元素管理 | 设计元素创建维护 | [designelements.md](./designelements.md) | ✅ 已完成 |
| 收款单 | 收款、审核、反审、作废、分账与取消分账，并联动银行卡与钱包余额 | [receivablebill.md](./receivablebill.md) | ✅ 已完成 |
| 损耗单 | 损耗、审核、反审、更新，并联动成品库存或原料库存扣减/回滚 | [wastagebill.md](./wastagebill.md) | ✅ 已完成 |
| 套式 | 套式创建与更新 |  | ⏳ 待分析 |
| 通知公告 | 公告打卡 |  | ⏳ 待分析 |
| 维修出库单 | 维修送厂出库、审核、反审、作废，并联动维修单重置、退厂登记池、库存与供应商钱包 | [maintainoutbill.md](./maintainoutbill.md) | ✅ 已完成 |
| 维修单 | 维修宿主链创建、审核、反审、一键调拨与进度聚合，并联动单件、退货、钱包、结算和调拨派生 | [maintainbill.md](./maintainbill.md) | ✅ 已完成 |
| 维修单报表 | 维修跟踪单分页/导出与 BI 汇总报表，按供应商、客户、部门、业务员、仓库和明细生命周期统计维修链 | [maintainbillreport.md](./maintainbillreport.md) | ✅ 已完成 |
| 维修单详情 | 维修明细查询、终结、流转仓位维护，并作为单件入库/调整来源池 | [maintainbilldetail.md](./maintainbilldetail.md) | ✅ 已完成 |
| 维修入库单 | 维修返厂回收入库、审核、反审、作废，并联动维修单累计、维修出库累计、库存与供应商钱包 | [maintaininbill.md](./maintaininbill.md) | ✅ 已完成 |
| 委外加工订单 | 委外订单创建、批量确认、工厂回复、终止核销 | [outsourcingorder.md](./outsourcingorder.md) | ✅ 已完成 |
| 文件存储 | 文件上传 |  | ⏳ 待分析 |
| 我的站内信 | 站内信已读 |  | ⏳ 待分析 |
| 新款管理 | 新款提报、审批、同步、批量修改 | [newsku.md](./newsku.md) | ✅ 已完成 |
| 业务分区 | 业务分区维护 | [businessarea.md](./businessarea.md) | ✅ 已完成 |
| 业务人员 | 业务人员维护 | [businessstaff.md](./businessstaff.md) | ✅ 已完成 |
| 盈亏调整单 | 盈亏调整创建、审核、反审、作废，并联动成品库存或原料库存回写 | [profitlossadjustbill.md](./profitlossadjustbill.md) | ✅ 已完成 |
| 用户个人中心 | 用户密码修改 |  | ⏳ 待分析 |
| 原料档案 | 原料基础资料维护 | [material.md](./material.md) | ✅ 已完成 |

## 3. 推荐分析顺序

1. 基础主数据与组织权限
   - 部门、业务分区、业务人员、仓库档案、客户档案、供应商档案、材质档案、大类档案、原料档案、款式库、套式、金价配置、单价信息维护、参数配置、菜单、权限、角色、认证
2. 核心订单与留存链路
   - 客户订单、客户订单-意向单、客留存、产品包、调拨单、成品调拨单、订单需求单
3. 核心库存与单件链路
   - 单件入库单、单件调整单、单件作废单、单件盘点单操作、库位调整单、盘点称重单、单件明细
4. 维修与委外链路
   - 维修单、维修单详情、维修入库单、维修出库单、维修单报表、委外加工订单
5. 结算、发货与核销链路
   - 出库结算单、出货单、订单核销、收款单、付款单、客户应收账调整、供应商应付账调整、调账单、结价单、盈亏调整单、损耗单
6. 来料、出料、退货、退厂链路
   - 客户来料单、客户出料单、客户退货单、供应商来料单、供应商出料单、成品入库单、成品退厂单、成品退厂登记单、成品转料单
7. SKU 设计与业务辅助
   - 新款管理、款式修改单、客户款式管理、款式收藏、设计元素管理、打印任务单、客户业务员修改单
8. 平台支撑与外围
   - 流程任务实例、定时任务、文件存储、通知公告、我的站内信、登录设备表、用户个人中心

## 4. 当前执行进度

### 4.1 已完成当前批次的核心交易链文档
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
- [maintainbillreport.md](/D:/ws/code/wms-api/docs/business/maintainbillreport.md)
- [maintaininbill.md](/D:/ws/code/wms-api/docs/business/maintaininbill.md)
- [maintainoutbill.md](/D:/ws/code/wms-api/docs/business/maintainoutbill.md)
- [maintainbilldetail.md](/D:/ws/code/wms-api/docs/business/maintainbilldetail.md)
- [cancelbill.md](/D:/ws/code/wms-api/docs/business/cancelbill.md)

### 4.2 当前阶段结论
1. 当前已补齐“客户订单 -> 客留存 -> 产品包 -> 调拨 -> 出库结算 -> 出货回调”主交易链。
2. 模块清单已把“已有正文”的条目统一收敛为 `✅ 已完成`，未关闭风险继续集中放在 8 节跟踪。
3. 资金与核销链已补到“结价单”，当前已确认收付款是双账本入口，客户/供应商调整是单边钱包链，调账单是双账户对敲钱包链，结价则是宿主单据里的反向冲回分支。
4. 盈亏调整单已确认属于库存链，不属于资金链：`3601` 走成品库存调整，`3602` 走原料库存与库存日志。
5. 损耗单已确认同属库存链，但只接受正向损耗：`3801` 扣成品库存，`3802` 扣原料库存并记日志。
6. 客户来料单已确认是“原料库存 + 材质钱包 + 现金钱包 + 结价冲回”的复合链，且材质钱包主账吃的是结算明细。
7. 供应商来料单与客户来料共表共服务，但钱包口径不同：采购入库按现金/材质二选一，提纯入库可同时影响两边。
8. 成品入库单已确认是“成品库存 + 供应商钱包”双链模块；其中 `0402/0403` 还会继续影响半成品回流、供应商领料台账和委外挂接。
9. 客户出料单与供应商出料单已确认同属“原料库存扣减 + 钱包扣减”链，但不同业务类型会把钱包动作收敛到现金、材质或两者同时变化。
10. 客户退货单已确认是“单件/成品回库 + 客户钱包回写 + 结价冲回 + 维修仓位联动”的复合链，主账口径来自退货结算明细。
11. 成品退厂单已确认是“成品库存扣减 + 供应商钱包扣减 + 来源挂接回写”的复合链；`0602` 还会继续同步半成品领料台账。
12. 成品退厂登记单已确认是“退厂候选池”，本身不动库存和钱包，真正关键字段是 `outOfWare` 与维修明细占用。
13. 成品转料单已确认不是钱包链，而是“成品库存出库 + 原料库存入库 + 单件转料状态流”三链组合。
14. 单件入库单已确认不是简单建档单，而是“建单件 + 单件库存入 + 成品库存出 + 需求单累计 + 维修单回写”的复合执行链。
15. 单件调整单已确认不是删旧建新，而是“改单件本体 + 单件库存旧出新入 + 成品库存差额补扣 + 维修新件信息回写”的联动修改链。
16. 单件作废单已确认不是删单件，而是“改单件状态为 DISCARD + 单件库存出库 + 成品库存回收”，反审再整条回滚。
17. 单件盘点单操作已确认本质是“盘点基线快照 + 批次采集 + 差异查询”模块，审核/反审当前只改单头状态，未看到正式库存过账。
18. 库位调整单已确认是“锁单件 -> 审核改库位 + moveStock -> 解锁/反审回滚”的执行链，不是简单改单件库位字段。
19. 单件明细已确认不是单独审批单，而是 `wh_item + wh_item_log` 这组单件主档/事件表的查询与运维入口。
20. 盘点称重单已确认不是库存执行单，而是“称重录入 + 系统补齐基线 + 审核计算盈亏差值”的盘点分析单。
21. 维修单已确认不是简单登记单，而是维修域宿主链：审核/反审会联动单件挂接、客户退货累计、钱包结算/结价冲回，以及部分分支下的库存动作。
22. 维修入库单已确认不是普通收货单，而是“维修出库回收入库 + 供应商钱包入账 + 维修单累计回写 + 单件返仓/库存恢复”的复合执行链。
23. 维修出库单已确认不是普通出库单，而是“在库货转维修态出库 + 供应商钱包扣减 + 维修单上一轮入库结果清空 + 退厂登记池推进”的复合执行链。
24. 维修单详情已确认不是单独审批单，而是维修域明细宿主模块；`finalization(...)` 会直接把维修明细推进到 `COMPLETED`，并在全部明细完成时把维修单头一起完结。
25. 维修单报表已确认真实入口是 `maintain-bill-bi`，不是独立 report 模块；它把维修出库链和维修入库链按统一维度做“送厂/回厂”双侧对照，并用维修明细串起生命周期查询。

### 4.3 下一优先模块
1. 委外加工订单
2. 基础主数据或维修域残余查询模块
3. 维修结算明细或维修域剩余辅助模块

## 5. 模块调用关系

- 产品包 → 客留存（创建、修改、预检查、作废时调用客留存相关服务）
- 产品包 → 客户订单（更新订单明细 `packId/localStatus` 并重算订单状态）
- 产品包 → 调拨单 / 成品调拨单（生成调拨单并审核，处理退回拒签/接收）
- 客留存 → 客户订单（创建、修改、合并、拆分、迁移时同步订单主表、订单明细和订单汇总）
- 客留存 → 单件 / 库存（锁定或解锁 EPC，检查成品库存）
- 客户订单 → 客户档案 / 业务人员 / 业务分区（创建订单时回填客户、业务员、业务经理信息）
- 客户订单 → 订单需求单（从 `WAIT_PICKING` 转为需求单时，将订单状态推进到 `WAIT_COMEBACK`）
- 出库结算单 → 产品包（挂销售单号、推进 `pack_status`，处理调拨中的产品包）
- 出库结算单 → 客户订单（推进订单明细到 `SETTLEMENT/BILLED`，实际发货时更新出货信息）
- 出库结算单 → 客留存（实际发货后结留存，重做时回滚）
- 出货单 → 客户订单（发货回调成功后把订单推进到 `DELIVERED/SHIPPED`）
- 订单核销 → 委外单（审核/反审时更新委外明细累计核销量，并把委外单头推进到 `待核销/已部分核销/核销完毕`）
- 订单核销 → 订单需求单（审核/反审时把需求单明细 `returnedNum/returnedWeight` 按合格核销量增减，并重新聚合需求单头）
- 收款单 → 网点银行卡（审核/反审时按明细 `bankCode` 增减银行卡余额，并写银行日志）
- 收款单 → 钱包（审核/反审时按原账户增减钱包余额，分账/取消分账时在多个账户之间重分配钱包归属）
- 付款单 → 网点银行卡（审核时按明细 `bankCode` 扣减银行卡余额，反审时逐条加回）
- 付款单 → 钱包（审核时按单头 `accountId` 增加现金钱包余额，反审时按单头反向扣回）
- 客户应收账调整 → 钱包（审核/反审时按明细逐条调整客户现金钱包与材质钱包，不经过银行卡）
- 供应商应付账调整 → 钱包（审核/反审时按明细逐条调整供应商现金钱包与材质钱包，不经过银行卡）
- 调账单 → 钱包（审核/反审时按单头汇总值在调出账户、调入账户之间成对调整现金钱包与材质钱包）
- 结价单 → 钱包（宿主单据审核时，`CLOSING_PRICE(02)` 先并入主账，再按相反方向对材质钱包做单独冲回）
- 盈亏调整单 → 成品库存（`3601` 审核/反审时通过 `updateGoodsStockAdjust(...)` 增减成品库存）
- 盈亏调整单 → 原料库存（`3602` 审核/反审时通过 `updateMaterialStock(...)` 增减原料库存并记录库存日志）
- 损耗单 → 成品库存（`3801` 审核/反审时通过 `updateGoodsStock(...)` 扣减或回滚成品库存）
- 损耗单 → 原料库存（`3802` 审核/反审时通过 `updateMaterialStock(...)` 扣减或回滚原料库存并记录日志）
- 客户来料单 → 原料库存（审核/反审时通过 `updateMaterialStock(...)` 增减原料库存并记录日志）
- 客户来料单 → 钱包（审核/反审时按结算明细回写材质钱包，并按 `totalSettleMoney` 反向回写现金钱包；结价项再单独冲回）
- 供应商来料单 → 原料库存（审核/反审时通过 `updateMaterialStock(...)` 增减原料库存并记录日志）
- 供应商来料单 → 钱包（审核/反审时按明细汇总口径回写供应商钱包，采购入库与提纯入库走不同工厂分支）
- 成品入库单 → 成品库存（审核/反审时通过 `updateGoodsStock(...)` 增减成品库存）
- 成品入库单 → 钱包（审核/反审时按明细金重和工费同时回写供应商材质钱包与现金钱包）
- 成品入库单 → 半成品回流 / 委外（`0402/0403` 会回写半成品回流累计、供应商领料台账，并维护委外/回流引用关系）
- 客户出料单 → 原料库存（审核/反审时通过 `updateMaterialStock(...)` 扣减或回补原料库存）
- 客户出料单 → 钱包（审核/反审时按 `2101/2102` 分支回写客户现金钱包或材质钱包）
- 供应商出料单 → 原料库存（审核/反审时通过 `updateMaterialStock(...)` 扣减或回补原料库存）
- 供应商出料单 → 钱包（审核/反审时按 `2301/2302/2303` 分支回写供应商材质钱包、现金钱包或两者同时变化）
- 客户退货单 → 单件/成品库存（审核时回库、反审时回滚；单件和成品走不同分支）
- 客户退货单 → 钱包（审核/反审时按退货结算明细回写客户钱包，并对结价项做单独冲回）
- 客户退货单 → 维修链（若明细挂维修单，审核/反审时同步改维修单明细流转仓库）
- 成品退厂登记单 → 维修链（`0503` 审核/反审/作废时会累计、回滚或释放 `wh_maintain_bill_detail` 占用）
- 成品退厂单 → 成品库存（审核时出库、反审时回滚）
- 成品退厂单 → 钱包（审核/反审时按金重与工费回写供应商材质钱包和现金钱包）
- 成品退厂单 → 退厂登记池（`0601` 会回写 `rejectBillId/outOfWare`）
- 成品退厂单 → 半成品回流台账（`0602` 会回写 `rejectBillNo`，并同步供应商领料台账）
- 成品转料单 → 原料库存（审核时按 `materialNo + destWareId` 增加原料库存，反审时回滚）
- 成品转料单 → 成品库存（审核时扣减成品库存，反审时回补）
- 成品转料单 → 单件转料单（带 `itemNo` 的明细会下钻到 `RecycleBillService.doSubmit(...)`）
- 单件入库单 → 单件表 / 单件日志（审核时批量创建 `wh_item` 并写 `wh_item_log`，反审时删除单件并写回滚日志）
- 单件入库单 → 库存（审核时“单件库存入 + 成品库存出”，反审时整条回滚）
- 单件入库单 → 订单需求单（审核/反审时回写 `entryBillNum/entryBillWeight` 并重算需求单头）
- 单件入库单 → 维修链（审核/反审时回写 `wh_maintain_bill_detail` 的入库数量、重量、单件编码与步骤仓位）
- 单件调整单 → 单件表（审批通过后直接覆盖更新已有 `wh_item`）
- 单件调整单 → 单件日志（审批通过后为每条明细写一出一入双日志）
- 单件调整单 → 库存（审批通过后同时做单件库存 `moveStock` 和成品库存重量差额调整）
- 单件调整单 → 维修链（审批结果回写时同步改维修明细 `newItemId/newItemNo/newGoldWeight/newWeight/newGrossWeight`）
- 单件作废单 → 单件表（审批通过时把单件状态由 `ON_WARE` 改为 `DISCARD`，反审时再回滚为 `ON_WARE`）
- 单件作废单 → 单件日志（审核/反审都会写单件作废与回滚日志）
- 单件作废单 → 库存（审核时“单件出、成品入”，反审时“成品出、单件入”）
- 单件盘点单操作 → 单件表（建单时整批冻结 `wh_item` 快照，扫码补录时也会回查真实单件）
- 单件盘点单操作 → 盘点快照表（建单后持续维护 `wh_item_inventory_bill_item / wh_item_inventory_bill_batch / wh_item_inventory_bill_stock`）
- 单件盘点单操作 → 报表入口（`pageInventory` 会与盘点称重单 `UNION` 展示）
- 库位调整单 → 单件表（审核/反审时回写 `wh_item.localId`）
- 库位调整单 → 库存（审核/反审时通过 `moveStock(...)` 调整库位库存分布）
- 库位调整单 → 外部同步（可通过 `/sync/{id}` 推送库位调整结果）
- 单件明细 → 单件日志（分页、导出、财务视图都会回填最近一条 `wh_item_log`）
- 单件明细 → 单件辅料（查询/导出/财务视图都会补 `wh_item_accessories`）
- 单件明细 → 单件执行链（是入库、调整、作废、库位、退货、转料等模块的共同结果落点）
- 盘点称重单 → 原料库存/成品库存（审核时只读取账面库存做差值对比，不直接改库存）
- 盘点称重单 → 盘点库存备份（成品称重审核时初始化 `inventoryWeighingBillStock` 基线快照）
- 维修单 → 单件表（审核/反审时挂接或解除 `maintainBillId/maintainBillDetailId/maintainBillNo`）
- 维修单 → 客户退货单（客户维修审核/反审时增减 `wh_return_detail.maintainNum`）
- 维修单 → 钱包（客户维修审核/反审时按维修结算明细回写客户钱包，并对 `CLOSING_PRICE(02)` 单独冲回）
- 维修单 → 调拨单 / 成品调拨单（一键调拨时直接派生并审核执行单）
- 维修入库单 → 维修单（审核/反审时回写 `inboundNum/inNum/repareFee/newItem.../replyStatus`，并重算维修单头进度）
- 维修入库单 → 维修出库单（审核/反审时增减维修出库明细 `inNum`，作废时解除 `pushMaintainInBillDetailId` 草稿挂接）
- 维修入库单 → 钱包（审核/反审时按金重和工费同时回写供应商材质钱包与现金钱包）
- 维修入库单 → 单件/成品库存（单件审核时返仓并增加单件库存，成品分支统一回写成品库存）
- 维修出库单 → 维修单（审核/反审时增减 `outNum`，并清空/恢复上一轮维修入库结果，再重算维修单头进度）
- 维修出库单 → 退厂登记池（草稿/审核/反审时持续回写 `outOfWare` 与 `maintainOutNum`）
- 维修出库单 → 钱包（审核/反审时按金重和工费同时扣减或回补供应商材质钱包与现金钱包）
- 维修出库单 → 单件/成品库存（审核时在库货转维修态出库，反审时回到在库）
- 维修单详情 → 维修单（终结时在“全部明细完成”条件下把单头 `replyStatus` 推进到 `COMPLETED`）
- 维修单详情 → 单件表（终结时回写 `maintainBillDetailReplyStatus`，下推单件调整时读取旧件本体）
- 维修单详情 → 维修入库明细（查询和下推时取“最近一次维修入库结果”作为当前返修结果基础）
- 维修单详情 → 单件入库单 / 单件调整单（作为后续生成草稿的来源池）
- 维修单报表 → 维修出库单 / 维修入库单（按统一维度聚合送厂与回厂两侧件数、金重、工费）
- 维修单报表 → 维修单 / 维修明细（以 `wh_maintain_bill_detail` 为主键串起维修生命周期、终结信息和状态展示）

## 6. 共享数据表

| 表名 | 写入方 | 读取方 |
|------|--------|--------|
| wh_pack | 产品包 | 产品包 |
| wh_pack_detail | 产品包 | 产品包 |
| wh_tray | 产品包 | 产品包 |
| wh_customer_retain | 客留存 | 客留存、产品包 |
| wh_customer_retain_record | 客留存 | 客留存、产品包 |
| wh_customer_order | 客户订单、客留存 | 客户订单、客留存、产品包 |
| wh_customer_order_detail | 客户订单、客留存 | 客户订单、客留存 |
| wh_outsourcing_order | 委外单、订单核销 | 订单核销 |
| wh_outsourcing_order_detail | 委外单、订单核销 | 订单核销 |
| wh_demand_order | 订单需求单、订单核销 | 订单需求单、订单核销 |
| wh_demand_order_detail | 订单需求单、订单核销 | 订单需求单、订单核销 |
| wh_receivable_bill | 收款单 | 收款单 |
| wh_receivable_bill_detail | 收款单 | 收款单 |
| wh_receivable_divide_bill | 收款单 | 收款单 |
| wh_payable_bill | 付款单 | 付款单 |
| wh_payable_bill_detail | 付款单 | 付款单 |
| wh_customer_adjust_bill | 客户应收账调整 | 客户应收账调整 |
| wh_customer_adjust_bill_detail | 客户应收账调整 | 客户应收账调整 |
| wh_supplier_adjust_bill | 供应商应付账调整 | 供应商应付账调整 |
| wh_supplier_adjust_bill_detail | 供应商应付账调整 | 供应商应付账调整 |
| wh_account_adjust_bill | 调账单 | 调账单 |
| wh_account_adjust_bill_detail | 调账单 | 调账单 |
| wh_profit_loss_adjust_bill | 盈亏调整单 | 盈亏调整单 |
| wh_profit_loss_adjust_detail | 盈亏调整单 | 盈亏调整单 |
| wh_wastage_bill | 损耗单 | 损耗单 |
| wh_wastage_detail | 损耗单 | 损耗单 |
| wh_material_inbound | 客户来料单、供应商来料单 | 客户来料单、供应商来料单 |
| wh_material_inbound_detail | 客户来料单、供应商来料单 | 客户来料单、供应商来料单 |
| wh_material_inbound_settle | 客户来料单、供应商来料单、结价单 | 客户来料单、供应商来料单、结价单 |
| wh_goods_inbound_bill | 成品入库单 | 成品入库单 |
| wh_goods_inbound_bill_detail | 成品入库单 | 成品入库单 |
| wh_goods_inbound_semis_record | 成品入库单 | 成品入库单 |
| wh_material_outbound | 客户出料单、供应商出料单 | 客户出料单、供应商出料单 |
| wh_material_outbound_detail | 客户出料单、供应商出料单 | 客户出料单、供应商出料单 |
| wh_return_bill | 客户退货单 | 客户退货单 |
| wh_return_detail | 客户退货单 | 客户退货单 |
| wh_return_settle | 客户退货单、结价单 | 客户退货单、结价单 |
| wh_goods_reject_record | 成品退厂登记单、成品退厂单 | 成品退厂登记单、成品退厂单 |
| wh_goods_reject_record_detail | 成品退厂登记单 | 成品退厂登记单、成品退厂单 |
| wh_goods_reject_bill | 成品退厂单 | 成品退厂单、成品退厂登记单 |
| wh_goods_reject_bill_detail | 成品退厂单 | 成品退厂单 |
| wh_goods_recycle_bill | 成品转料单 | 成品转料单 |
| wh_goods_recycle_detail | 成品转料单 | 成品转料单 |
| wh_recycle_bill | 单件转料单、成品转料单 | 单件转料单、成品转料单 |
| wh_recycle_detail | 单件转料单、成品转料单 | 单件转料单、成品转料单 |
| wh_entry_bill | 单件入库单 | 单件入库单 |
| wh_entry_bill_detail | 单件入库单 | 单件入库单 |
| wh_entry_bill_detail_accessories | 单件入库单 | 单件入库单 |
| wh_item | 单件入库单、单件转料单 | 单件入库单、单件转料单、客户退货单 |
| wh_item_log | 单件入库单、单件转料单 | 单件入库单、单件转料单 |
| wh_adjust_bill | 单件调整单 | 单件调整单 |
| wh_adjust_bill_detail | 单件调整单 | 单件调整单 |
| wh_adjust_bill_detail_accessories | 单件调整单 | 单件调整单 |
| wh_cancel_bill | 单件作废单 | 单件作废单 |
| wh_cancel_bill_detail | 单件作废单 | 单件作废单 |
| wh_item_inventory_bill | 单件盘点单操作 | 单件盘点单操作 |
| wh_item_inventory_bill_batch | 单件盘点单操作 | 单件盘点单操作 |
| wh_item_inventory_bill_item | 单件盘点单操作 | 单件盘点单操作 |
| wh_item_inventory_bill_stock | 单件盘点单操作 | 单件盘点单操作 |
| wh_shelves_bill | 库位调整单 | 库位调整单 |
| wh_shelves_bill_detail | 库位调整单 | 库位调整单 |
| wh_item | 单件明细、单件入库单、单件调整单、单件作废单、库位调整单 | 单件明细及相关单件执行链 |
| wh_item_log | 单件明细、单件入库单、单件调整单、单件作废单、库位调整单 | 单件明细及相关单件执行链 |
| wh_inventory_weighing_bill | 盘点称重单 | 盘点称重单 |
| wh_inventory_weighing_detail | 盘点称重单 | 盘点称重单 |
| wh_sale_settle | 出库结算单 / 结价单 | 出库结算单 / 结价单 |
| wh_return_settle | 客户退货单 / 结价单 | 客户退货单 / 结价单 |
| wh_shop_bank | 收款单、付款单 | 收款单、付款单 |
| wh_wallet | 收款单、付款单、客户应收账调整、供应商应付账调整、调账单、结价单、客户来料单、供应商来料单、成品入库单、成品退厂单 | 收款单、付款单、客户应收账调整、供应商应付账调整、调账单、结价单、客户来料单、供应商来料单、成品入库单、成品退厂单 |
| wh_material_stock | 盈亏调整单、损耗单、客户来料单、供应商来料单 | 盈亏调整单、损耗单、客户来料单、供应商来料单 |
| wh_material_stock_log | 盈亏调整单、损耗单、客户来料单、供应商来料单、成品转料单 | 盈亏调整单、损耗单、客户来料单、供应商来料单、成品转料单 |
| wh_sku_stock | 成品入库单、成品退厂单、成品转料单 | 成品入库单、成品退厂单、成品转料单 |
| wh_maintain_bill | 维修单 | 维修单、维修入库单、维修出库单、维修详情 |
| wh_maintain_bill_detail | 维修单 | 维修单、维修入库单、维修出库单、维修详情、成品退厂登记单 |
| wh_maintain_in_bill | 维修入库单 | 维修入库单、维修出库单 |
| wh_maintain_in_bill_detail | 维修入库单 | 维修入库单、维修出库单、维修详情 |
| wh_maintain_out_bill | 维修出库单 | 维修出库单、维修入库单 |
| wh_maintain_out_bill_detail | 维修出库单 | 维修出库单、维修入库单、维修详情 |

## 7. 图表与总表入口

### 7.1 实体关系图
- [核心实体总览](/D:/ws/code/wms-api/docs/business/model/entity-overview.md)
- [订单主链实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-order-chain.md)
- [调拨主链实体图](/D:/ws/code/wms-api/docs/business/model/entity-transfer-chain.md)
- [订单核销链实体图](/D:/ws/code/wms-api/docs/business/model/entity-writeoff-chain.md)
- [收款单资金链实体图](/D:/ws/code/wms-api/docs/business/model/entity-receivable-chain.md)
- [付款单资金链实体图](/D:/ws/code/wms-api/docs/business/model/entity-payable-chain.md)
- [客户应收账调整钱包链实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-adjust-chain.md)
- [供应商应付账调整钱包链实体图](/D:/ws/code/wms-api/docs/business/model/entity-supplier-adjust-chain.md)
- [调账单双账户钱包链实体图](/D:/ws/code/wms-api/docs/business/model/entity-account-adjust-chain.md)
- [结价链实体图](/D:/ws/code/wms-api/docs/business/model/entity-closing-price-chain.md)
- [盈亏调整单库存链实体图](/D:/ws/code/wms-api/docs/business/model/entity-profit-loss-adjust-chain.md)
- [损耗单库存链实体图](/D:/ws/code/wms-api/docs/business/model/entity-wastage-chain.md)
- [客户来料单实体图](/D:/ws/code/wms-api/docs/business/model/entity-material-inbound-chain.md)
- [供应商来料单实体图](/D:/ws/code/wms-api/docs/business/model/entity-supplier-material-inbound-chain.md)
- [成品入库单实体图](/D:/ws/code/wms-api/docs/business/model/entity-goods-inbound-chain.md)
- [客户出料单实体图](/D:/ws/code/wms-api/docs/business/model/entity-material-outbound-chain.md)
- [供应商出料单实体图](/D:/ws/code/wms-api/docs/business/model/entity-supplier-material-outbound-chain.md)
- [客户退货单实体图](/D:/ws/code/wms-api/docs/business/model/entity-return-chain.md)
- [成品退厂单实体图](/D:/ws/code/wms-api/docs/business/model/entity-goods-reject-chain.md)
- [成品退厂登记单实体图](/D:/ws/code/wms-api/docs/business/model/entity-goods-reject-record-chain.md)
- [成品转料单实体图](/D:/ws/code/wms-api/docs/business/model/entity-goods-recycle-chain.md)
- [单件入库单实体图](/D:/ws/code/wms-api/docs/business/model/entity-entry-bill-chain.md)
- [单件调整单实体图](/D:/ws/code/wms-api/docs/business/model/entity-adjust-bill-chain.md)
- [单件作废单实体图](/D:/ws/code/wms-api/docs/business/model/entity-cancel-bill-chain.md)
- [单件盘点单实体图](/D:/ws/code/wms-api/docs/business/model/entity-item-inventory-chain.md)
- [库位调整单实体图](/D:/ws/code/wms-api/docs/business/model/entity-shelves-chain.md)
- [单件明细实体图](/D:/ws/code/wms-api/docs/business/model/entity-item-domain-chain.md)
- [盘点称重单实体图](/D:/ws/code/wms-api/docs/business/model/entity-inventory-weighing-chain.md)
- [维修单实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-bill-chain.md)
- [维修单报表实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-report-chain.md)
- [维修入库单实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-in-chain.md)
- [维修出库单实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-out-chain.md)
- [维修单详情实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-detail-chain.md)
- [维修结算明细实体图](/D:/ws/code/wms-api/docs/business/model/entity-maintain-settle-chain.md)
- [委外加工订单实体图](/D:/ws/code/wms-api/docs/business/model/entity-outsourcing-order-chain.md)
- [委外加工订单明细实体图](/D:/ws/code/wms-api/docs/business/model/entity-outsourcing-detail-chain.md)
- [结算方式实体图](/D:/ws/code/wms-api/docs/business/model/entity-settlement-chain.md)
- [仓库结构实体图](/D:/ws/code/wms-api/docs/business/model/entity-warehouse-structure-chain.md)
- [客户档案实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-domain-chain.md)
- [供应商档案实体图](/D:/ws/code/wms-api/docs/business/model/entity-supplier-domain-chain.md)
- [材质档案实体图](/D:/ws/code/wms-api/docs/business/model/entity-texture-domain-chain.md)
- [大类档案实体图](/D:/ws/code/wms-api/docs/business/model/entity-bigclass-domain-chain.md)
- [品类结构实体图](/D:/ws/code/wms-api/docs/business/model/entity-category-structure-chain.md)
- [产品资料实体图](/D:/ws/code/wms-api/docs/business/model/entity-product-domain-chain.md)
- [SKU 主档实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-domain-chain.md)
- [供应商款式 BOM 实体图](/D:/ws/code/wms-api/docs/business/model/entity-supplier-sku-chain.md)
- [SKU 规格实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-specs-chain.md)
- [SKU 审批域实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-domain-chain.md)
- [SKU 审批明细实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-detail-chain.md)
- [SKU 审批 BOM 缓存实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-bom-cache-chain.md)
- [SKU 审批辅助链实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-approval-support-chain.md)
- [SKU 规则主数据实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-rule-master-chain.md)
- [系列与套系映射实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-series-set-chain.md)
- [SKU 编码规则实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-rule-chain.md)
- [抄送配置实体图](/D:/ws/code/wms-api/docs/business/model/entity-copy-setting-chain.md)
- [SKU 供应商索引实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-supplier-index-chain.md)
- [客户侧 SKU 关系域实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-sku-domain-chain.md)
- [款式模号实体图](/D:/ws/code/wms-api/docs/business/model/entity-sku-model-chain.md)
- [共享库存实体图](/D:/ws/code/wms-api/docs/business/model/entity-share-stock-chain.md)
- [共享方实体图](/D:/ws/code/wms-api/docs/business/model/entity-share-party-chain.md)
- [共享流水与老庙接口痕迹实体图](/D:/ws/code/wms-api/docs/business/model/entity-share-flow-lm-api-chain.md)
- [老庙订单与回调实体图](/D:/ws/code/wms-api/docs/business/model/entity-lm-order-callback-chain.md)
- [接口同步任务实体图](/D:/ws/code/wms-api/docs/business/model/entity-sync-task-chain.md)
- [旧版共享库存域实体图](/D:/ws/code/wms-api/docs/business/model/entity-stock-share-domain-chain.md)
- [客户字印实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-marking-chain.md)
- [客锁管理实体图](/D:/ws/code/wms-api/docs/business/model/entity-customer-lock-chain.md)

### 7.2 状态机图
- [客户订单状态机图](/D:/ws/code/wms-api/docs/business/model/customer-order-state.md)
- [客留存状态机图](/D:/ws/code/wms-api/docs/business/model/customerretain-state.md)
- [产品包状态机图](/D:/ws/code/wms-api/docs/business/model/pack-state.md)
- [出库结算单状态机图](/D:/ws/code/wms-api/docs/business/model/sale-bill-state.md)
- [出货单状态机图](/D:/ws/code/wms-api/docs/business/model/delivery-state.md)
- [调拨状态机图](/D:/ws/code/wms-api/docs/business/model/transfer-state.md)
- [订单核销状态机图](/D:/ws/code/wms-api/docs/business/model/order-writeoff-state.md)
- [收款单状态机图](/D:/ws/code/wms-api/docs/business/model/receivable-bill-state.md)
- [付款单状态机图](/D:/ws/code/wms-api/docs/business/model/payable-bill-state.md)
- [客户应收账调整状态机图](/D:/ws/code/wms-api/docs/business/model/customer-adjust-bill-state.md)
- [供应商应付账调整状态机图](/D:/ws/code/wms-api/docs/business/model/supplier-adjust-bill-state.md)
- [调账单状态机图](/D:/ws/code/wms-api/docs/business/model/account-adjust-bill-state.md)
- [结价依附状态图](/D:/ws/code/wms-api/docs/business/model/closing-price-state.md)
- [盈亏调整单状态机图](/D:/ws/code/wms-api/docs/business/model/profit-loss-adjust-bill-state.md)
- [损耗单状态机图](/D:/ws/code/wms-api/docs/business/model/wastage-bill-state.md)
- [客户来料单状态机图](/D:/ws/code/wms-api/docs/business/model/material-inbound-state.md)
- [供应商来料单状态机图](/D:/ws/code/wms-api/docs/business/model/supplier-material-inbound-state.md)
- [成品入库单状态机图](/D:/ws/code/wms-api/docs/business/model/goods-inbound-bill-state.md)
- [客户出料单状态机图](/D:/ws/code/wms-api/docs/business/model/material-outbound-state.md)
- [供应商出料单状态机图](/D:/ws/code/wms-api/docs/business/model/supplier-material-outbound-state.md)
- [客户退货单状态机图](/D:/ws/code/wms-api/docs/business/model/return-bill-state.md)
- [成品退厂单状态机图](/D:/ws/code/wms-api/docs/business/model/goods-reject-bill-state.md)
- [成品退厂登记单状态机图](/D:/ws/code/wms-api/docs/business/model/goods-reject-record-state.md)
- [成品转料单状态机图](/D:/ws/code/wms-api/docs/business/model/goods-recycle-bill-state.md)
- [单件入库单状态机图](/D:/ws/code/wms-api/docs/business/model/entry-bill-state.md)
- [单件调整单状态机图](/D:/ws/code/wms-api/docs/business/model/adjust-bill-state.md)
- [单件作废单状态机图](/D:/ws/code/wms-api/docs/business/model/cancel-bill-state.md)
- [单件盘点单状态机图](/D:/ws/code/wms-api/docs/business/model/item-inventory-bill-state.md)
- [库位调整单状态机图](/D:/ws/code/wms-api/docs/business/model/shelves-bill-state.md)
- [单件明细状态/能力图](/D:/ws/code/wms-api/docs/business/model/item-entity-state.md)
- [盘点称重单状态机图](/D:/ws/code/wms-api/docs/business/model/inventory-weighing-bill-state.md)
- [维修单状态机图](/D:/ws/code/wms-api/docs/business/model/maintain-bill-state.md)
- [维修单报表能力图](/D:/ws/code/wms-api/docs/business/model/maintain-bill-report-state.md)
- [维修入库单状态机图](/D:/ws/code/wms-api/docs/business/model/maintain-in-bill-state.md)
- [维修出库单状态机图](/D:/ws/code/wms-api/docs/business/model/maintain-out-bill-state.md)
- [维修单详情状态/能力图](/D:/ws/code/wms-api/docs/business/model/maintain-bill-detail-state.md)
- [维修结算明细状态/依附图](/D:/ws/code/wms-api/docs/business/model/maintain-bill-settle-state.md)
- [委外加工订单状态机图](/D:/ws/code/wms-api/docs/business/model/outsourcing-order-state.md)
- [委外加工订单明细能力图](/D:/ws/code/wms-api/docs/business/model/outsourcing-order-detail-state.md)
- [结算方式能力图](/D:/ws/code/wms-api/docs/business/model/settlement-state.md)
- [仓库结构能力图](/D:/ws/code/wms-api/docs/business/model/warehouse-structure-state.md)
- [客户档案能力图](/D:/ws/code/wms-api/docs/business/model/customer-entity-state.md)
- [供应商档案能力图](/D:/ws/code/wms-api/docs/business/model/supplier-entity-state.md)
- [材质档案能力图](/D:/ws/code/wms-api/docs/business/model/texture-entity-state.md)
- [大类档案能力图](/D:/ws/code/wms-api/docs/business/model/bigclass-entity-state.md)
- [品类结构能力图](/D:/ws/code/wms-api/docs/business/model/category-structure-state.md)
- [产品资料能力图](/D:/ws/code/wms-api/docs/business/model/product-entity-state.md)
- [SKU 主档能力图](/D:/ws/code/wms-api/docs/business/model/sku-entity-state.md)
- [供应商款式 BOM 能力图](/D:/ws/code/wms-api/docs/business/model/supplier-sku-bom-state.md)
- [SKU 规格能力图](/D:/ws/code/wms-api/docs/business/model/sku-specs-state.md)
- [SKU 审批域能力图](/D:/ws/code/wms-api/docs/business/model/sku-approval-domain-state.md)
- [SKU 审批明细能力图](/D:/ws/code/wms-api/docs/business/model/sku-approval-detail-state.md)
- [SKU 审批 BOM 缓存能力图](/D:/ws/code/wms-api/docs/business/model/sku-approval-bom-cache-state.md)
- [SKU 审批辅助链能力图](/D:/ws/code/wms-api/docs/business/model/sku-approval-support-state.md)
- [SKU 规则主数据能力图](/D:/ws/code/wms-api/docs/business/model/sku-rule-master-state.md)
- [系列与套系映射能力图](/D:/ws/code/wms-api/docs/business/model/sku-series-set-state.md)
- [SKU 编码规则能力图](/D:/ws/code/wms-api/docs/business/model/sku-rule-state.md)
- [抄送配置能力图](/D:/ws/code/wms-api/docs/business/model/copy-setting-state.md)
- [SKU 供应商索引能力图](/D:/ws/code/wms-api/docs/business/model/sku-supplier-index-state.md)
- [客户侧 SKU 关系域能力图](/D:/ws/code/wms-api/docs/business/model/customer-sku-domain-state.md)
- [款式模号能力图](/D:/ws/code/wms-api/docs/business/model/sku-model-state.md)
- [共享库存能力图](/D:/ws/code/wms-api/docs/business/model/share-stock-state.md)
- [共享方能力图](/D:/ws/code/wms-api/docs/business/model/share-party-state.md)
- [共享流水与老庙接口痕迹能力图](/D:/ws/code/wms-api/docs/business/model/share-flow-lm-api-state.md)
- [老庙订单与回调能力图](/D:/ws/code/wms-api/docs/business/model/lm-order-callback-state.md)
- [接口同步任务能力图](/D:/ws/code/wms-api/docs/business/model/sync-task-state.md)
- [旧版共享库存域能力图](/D:/ws/code/wms-api/docs/business/model/stock-share-domain-state.md)
- [客户字印能力图](/D:/ws/code/wms-api/docs/business/model/customer-marking-state.md)
- [客锁管理能力图](/D:/ws/code/wms-api/docs/business/model/customer-lock-state.md)

### 7.3 总表类产物
- [状态矩阵总表](/D:/ws/code/wms-api/docs/business/state-matrix.md)
- [高频业务口径总表](/D:/ws/code/wms-api/docs/business/business-metrics.md)
- [跨模块回写清单](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)
- [外部系统交互清单](/D:/ws/code/wms-api/docs/business/external-interactions.md)
- [字段语义表](/D:/ws/code/wms-api/docs/business/field-semantics.md)
- [变更影响清单](/D:/ws/code/wms-api/docs/business/change-impact.md)

## 8. 全局集中待办和回填

| 标记 | 来源模块 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|---------|------|----------|----------|
| ⚠️ | 客留存 | `cancel` |  | “重新上柜”文案对应的状态实际更新为 `RECYCLED` | 待确认 | 人工确认 `RECYCLED` 是否就是上柜完成态 |
| ⚠️ | 客留存 | `migrateRetain` |  | 通过修改 `loginUser.wareIds` 放宽仓库权限 | 待确认 | 人工确认是否允许运行时修改登录上下文 |
| ⚠️ | 客户订单 | `customerOrderStatusChange(Collection<Long>)` | 客户订单 | 批量重算时遇到一张不可处理订单会直接 `return`，可能导致同批次其余订单未重算 | 待确认 | 结合调用方批量传参场景确认是否应改为 `continue` |
| ⚠️ | 出库结算单 | `deliveryRequest` | 出库结算单 | 接口名和状态枚举存在“申请发货”含义，但实现直接把状态改成 `DELIVERY_ALLOW` | 待确认 | 人工确认 `DELIVERY_REQUESTING` 是否已废弃 |
| ⚠️ | 出库结算单 | `staffNoDelivered` | 出库结算单 | 同一次状态流里先 `updateByIdAndStatus()`，随后又执行一次 `updateById()` | 待确认 | 确认第二次更新是否属于冗余逻辑 |
| ⚠️ | 订单核销 | `OrderWriteoffApiImpl.updateProcessResult` | 订单核销 / BPM | 已定义 `PROCESS_KEY = oa_orderWriteoff`，但审批结果回写逻辑被整段注释，当前看不到流程结果如何驱动业务单 | 待确认 | 确认流程审批是否已废弃，或真实回调是否已迁移到其他入口 |
| ⚠️ | 收款单 | `deleteReceivableBill` | 收款单 / 收款单明细 | 仅物理删除主表，未看到状态校验，也未同步清理明细和资金侧痕迹 | 待确认 | 确认接口是否仍对外使用，是否应限制为草稿态并补齐级联清理 |
| ⚠️ | 收款单 | `discard` | 收款单 / 分账 | 只禁止 `CONFIRM`，未禁止 `DIVIDE`，已分账单据可能被直接作废而不回滚钱包分配 | 待确认 | 确认作废前是否应强制先取消分账 |
| ⚠️ | 付款单 | `deletePayableBill` | 付款单 / 付款单明细 | 仅物理删除主表，未看到状态校验，也未同步删除明细与资金侧痕迹 | 待确认 | 确认接口是否仍对外开放，是否应限制为草稿态并补齐级联清理 |
| ⚠️ | 付款单 | `confirm` | 付款单 | 代码只拦“已审不能重复审核”，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()`，把可审核状态收敛到未审/已反审 |
| ⚠️ | 客户应收账调整 | `deleteCustomerAdjustBill` | 客户应收账调整 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅用于草稿态，是否应补状态校验 |
| ⚠️ | 客户应收账调整 | `confirm` | 客户应收账调整 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | 供应商应付账调整 | `deleteSupplierAdjustBill` | 供应商应付账调整 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅用于草稿态，是否应补状态校验 |
| ⚠️ | 供应商应付账调整 | `confirm` | 供应商应付账调整 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | 调账单 | `deleteAccountAdjustBill` | 调账单 / 调账明细 | 只删主表，没看到级联删除明细，也没见状态限制 | 待确认 | 确认删除接口是否只允许草稿态，是否应补明细级联清理 |
| ⚠️ | 调账单 | `confirm` | 调账单 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态，理论上 `DISCARD` 也可能再次被审核 | 待确认 | 确认是否应复用 `BillStatusEnum.confirmCheck()` |
| ⚠️ | 盈亏调整单 | `deleteProfitLossAdjustBill` | 盈亏调整单 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | 盈亏调整单 | `createProfitLossAdjustBill/updateProfitLossAdjustBill` | 盈亏调整单明细 | `validDetails()` 已写好但被注释，当前未强制拦截空值或 0 值异常明细 | 待确认 | 确认这是有意放宽还是漏接校验 |
| ⚠️ | 盈亏调整单 | `updateProfitLossAdjustBill` | 盈亏调整单 | 重算 `totalGoldWeight` 时未过滤 `null`，更新请求若出现空金重存在空指针风险 | 待确认 | 确认前端是否保证非空，或后端补空值过滤 |
| ⚠️ | 损耗单 | `deleteWastageBill` | 损耗单 / 损耗明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | 损耗单 | `doGoodsStock` | 损耗单 / 成品库存 | 饰品损耗传入 `stock = 0`，只按金重扣库存；需确认业务上是否明确“不扣件数只扣重量” | 待确认 | 确认饰品损耗口径是否只看金重 |
| ⚠️ | 客户来料单 | `getCloseWalletData` | 客户来料单 / 结价 | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定 `settleNo` | 待确认 | 确认是否应改成按 `settleNo=02` 识别 |
| ⚠️ | 客户来料单 | `doCreateMaterialInboundBill` | 客户来料单 | `totalStockWeight` 建单时直接取负，报表若按“本次来料重”理解容易看反 | 待确认 | 确认前端/报表是否已按欠料变化量解释 |
| ⚠️ | 供应商来料单 | `InboundSupplierBuy#doBizType` | 供应商来料单 | 采购入库用“现金是否大于 0”决定只走现金还是只走材质钱包，隐式规则较强 | 待确认 | 确认业务上是否始终接受这种二选一 |
| ⚠️ | 成品入库单 | `deleteGoodsInboundBill` | 成品入库单 | 删除只看到了主表和明细，未见半成品记录、附件、配件的级联清理，也未见状态限制 | 待确认 | 确认删除接口是否只允许草稿态并补齐级联删除 |
| ⚠️ | 成品入库单 | `convertInbound` | 成品入库单 / 委外 | “根据委外明细转成品入库明细”方法目前直接返回空列表，整段真实实现被注释 | 待确认 | 确认该入口是否已废弃或迁移 |
| ⚠️ | 成品入库单 | `confirm/infirm` | 成品入库单 | 库存更新调用里统一传 `BizTypeEnum.G_PURCHASE_IN_STORAGE`，即使当前单据可能是 `0402/0403` | 待确认 | 确认库存日志是否因此丢失真实业务类型 |
| ⚠️ | 客户出料单 | `confirm` | 客户出料单 | 审核校验仍通过 `billName` 文案分支判断客户/供应商，不是稳定的 `billType/bizType` | 待确认 | 确认是否存在文案变更导致分支失效的风险 |
| ⚠️ | 供应商出料单 | `doCreateMaterialOutboundBill` | 供应商出料单 | `2301/2302/2303` 的金额公式全部硬编码在同一方法里，后续改造容易串业务口径 | 待确认 | 变更前需按业务类型逐项回归 |
| ⚠️ | 客户退货单 | `handlerCloseWallet` | 客户退货单 / 结价 | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定 `settleNo` | 待确认 | 确认是否应统一改成按 `settleNo=02` 识别 |
| ⚠️ | 客户退货单 | `deleteReturnBill` | 客户退货单 | 删除逻辑未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | 成品退厂单 | `delete(Long id)` | 成品退厂单 | 删除接口当前直接返回 `false`，没有真实实现 | 待确认 | 确认前端是否已禁用删除，或后端是否遗漏实现 |
| ⚠️ | 成品退厂单 | `doOutOfWareRejectRecord` | 成品退厂单 / 退厂登记单 | 反审时把登记池状态回写成 `OUTING`，不是 `NOT_OUT` | 待确认 | 确认这是否表示“仍被退厂单占用但已撤销出库” |
| ⚠️ | 成品退厂登记单 | `delete(Long id)` | 成品退厂登记单 | 删除直接物理删主表和明细，未见状态限制，也未释放维修占用 | 待确认 | 确认删除接口是否只允许草稿态，且是否应补 `recordId` 释放 |
| ⚠️ | 成品退厂登记单 | `confirm(Long id)` | 成品退厂登记单 | 审核只拦重复审核，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 | 确认是否应统一改成 `BillStatusEnum.confirmCheck(...)` |
| ⚠️ | 单件入库单 | `deleteEntryBill` | 单件入库单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | 单件入库单 | `confirm` | 单件入库单 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态再次审核 | 待确认 | 确认是否应统一改成 `BillStatusEnum.confirmCheck(...)` |
| ⚠️ | 单件入库单 | `confirm/infirm` | 单件入库单 | 成品库存更新统一传 `SG_PURCHASE_IN_STORAGE`，即使真实单据可能是 `0801/0803` | 待确认 | 确认库存日志是否会丢真实业务类型 |
| ⚠️ | 单件入库单 | `infirm` | 单件入库单 / 单件表 | 反审直接物理删除 `wh_item`，不是状态回滚 | 待确认 | 确认历史追溯是否只依赖 `wh_item_log` |
| ⚠️ | 单件调整单 | `deleteAdjustBill` | 单件调整单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | 单件调整单 | `discard` | 单件调整单 | 单据不存在时抛的是 `TRANSFER_BILL_NOT_EXISTS`，错误码串到调拨单模块 | 待确认 | 确认是否应改成调整单自己的错误码 |
| ⚠️ | 单件调整单 | `updateProcessResult` | 单件调整单 / 维修链 | 未通过审批时也会先执行 `dealMaintain()` 与 `unlockByEpcList()` | 待确认 | 确认驳回时提前释放锁和回写维修单是否符合预期 |
| ⚠️ | 单件调整单 | `updateGoodsStockAdjust` | 单件调整单 / 成品库存 | 成品库存差额只看重量差，`num` 固定为 `0` | 待确认 | 确认业务上是否明确“不按件数调成品库存” |
| ⚠️ | 单件作废单 | `deleteCancelBill` | 单件作废单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | 单件作废单 | `confirm` | 单件作废单 / BPM | 方法名叫“审核”，真实语义却是发起审批流程并把单据推到 `CONFIRMING` | 待确认 | 确认前端/调用方是否把它当成立即审核通过 |
| ⚠️ | 单件作废单 | `infirm` | 单件作废单 | 跨日权限错误文案写成“产品包跨日审核反审权限” | 待确认 | 修正文案，避免串模块误导排障 |
| ⚠️ | 单件作废单 | `infirm` | 单件作废单 | 反审状态校验失败时提示“必须是核销状态反审核”，错误文案串到订单核销模块 | 待确认 | 修正异常提示，避免误导业务和测试 |
| ⚠️ | 单件盘点单操作 | `approved` | 单件盘点单操作 | 审核当前只改单头状态，没看到依据差异正式回写 `wh_item`、库存或日志 | 待确认 | 确认差异生效是否在别的模块、定时任务或人工流程中执行 |
| ⚠️ | 单件盘点单操作 | `returnApproved` | 单件盘点单操作 | 反审只允许撤当天审核单据，且没有跨日权限配置分支 | 待确认 | 确认业务上是否明确禁止隔日反审 |
| ⚠️ | 单件盘点单操作 | `createInventoryBill` | 单件盘点单操作 | 24 小时排他校验用 `bill_status != '4'`，对状态码含义存在硬编码依赖 | 待确认 | 确认 `4` 是否稳定等于作废态 |
| ⚠️ | 单件盘点单操作 | `deleteItemInventoryBill` | 单件盘点单操作 | 通用 CRUD 删除只删主表，未见批次、明细、库存备份级联清理 | 待确认 | 确认该删除接口是否已废弃或仅为代码生成残留 |
| ⚠️ | 库位调整单 | `deleteShelvesBill` | 库位调整单 | 删除只删主表，未见明细级联、EPC 解锁和状态限制 | 待确认 | 确认该接口是否已废弃，或补完整删除保护链 |
| ⚠️ | 库位调整单 | `updateShelvesBill` | 库位调整单 | 更新时先算出 `shelves.setTotal...`，但真正 `updateById` 用的是 `updateObj`，汇总字段可能没写回 | 待确认 | 确认单头合计是否会被更新请求覆盖丢失 |
| ⚠️ | 库位调整单 | `discard` | 库位调整单 | 作废分支里 `itemMap` 以 `itemId` 为 key，却按 `d.getEpc()` 取值，存在取空风险 | 待确认 | 修正为按 `itemId` 取 `ItemDO`，避免解锁日志异常 |
| ⚠️ | 库位调整单 | `ShelvesBillDetailMapper.selectList` | 库位调整单 | 导出查询最后直接 `return selectList()`，前面构造的过滤条件没有真正使用 | 待确认 | 修正为 `return selectList(wrapperX)`，避免导出全表 |
| ⚠️ | 单件明细 | `unlockByEpcList` | 单件明细 | 批量解锁不校验当前业务状态，可能把别的链路锁住的单件直接解开 | 待确认 | 确认是否所有调用方都能保证只解自己持有的锁 |
| ⚠️ | 单件明细 | `ItemMapper.selectList(ItemExportReqVO)` | 单件明细 | 导出口径里 `partsFee` 过滤写成了 `getPartsPrice`，字段映射看起来不一致 | 待确认 | 确认这里是否应改成 `getPartsFee` |
| ⚠️ | 单件明细 | `getItemByItemNo` | 单件明细 | 方法名按条码查，但实现是 `epc = itemNo OR item_no = itemNo`，语义比名字更宽 | 待确认 | 确认调用方是否依赖这种“EPC/条码混查”行为 |
| ⚠️ | 单件明细 | `releaseCustomerLock` | 单件明细 / 单件日志 | 同一流程里既 `createItemLog(...)` 又手工 `save(itemLog)`，客锁释放日志可能重复 | 待确认 | 确认是否确实会落两条同义日志 |
| ⚠️ | 盘点称重单 | `confirm` | 盘点称重单 | 审核当前只计算并保存盈亏差值，没看到正式回写 `wh_sku_stock` 或 `wh_material_stock` | 待确认 | 确认差异生效是否落在别的盘点处理流程 |
| ⚠️ | 盘点称重单 | `deleteInventoryWeighingBill` | 盘点称重单 | 删除主表和明细时没看到状态限制 | 待确认 | 确认是否只允许删除草稿/反审态 |
| ⚠️ | 盘点称重单 | `fillData/handleDetailList` | 盘点称重单 | 详情页会过滤 `isGen = 1` 明细，但汇总又依赖全量明细，前后端容易对不上口径 | 待确认 | 确认页面汇总是否明确按“全量差值”展示 |
| ⚠️ | 盘点称重单 | `doProfitLossAccessory` | 盘点称重单 | 只有成品称重审核时才初始化 `inventoryWeighingBillStock` 账面备份，原料称重未见对应备份表动作 | 待确认 | 确认原料侧是否不需要单据级库存快照 |
| ⚠️ | 维修单 | `deleteMaintainBill` | 维修单 / 维修明细 | 删除当前只删主表，未见明细级联、状态限制和来源挂接清理 | 待确认 | 确认是否只允许草稿态删除，并补齐明细/挂接清理 |
| ⚠️ | 维修单 | `confirm` / `infirm` | 维修单 | 单头审核态只改 `billStatus`，真正业务动作分散在 `MaintainBillConfirmBusiness`，维护时很容易只改一半链路 | 待确认 | 后续改造必须把 Service 层和 ConfirmBusiness 视为同一条事务链 |
| ⚠️ | 维修单 | `setToCounter` | 维修单 | 当前只拦 `replyStatus = 4`，未见对未审核、已作废等异常状态的显式限制 | 待确认 | 确认“设置到柜”是否应补单头审核态校验 |
| ⚠️ | 维修单 | `updateTotalColumn` | 维修单 | 单头 `replyStatus` 完全依赖累计量聚合，若下游维修入库/结算/上柜回写漏一处，维修单进度会失真 | 待确认 | 后续分析维修入库单、维修出库单、维修详情时重点核对累计字段来源 |
| ⚠️ | 维修入库单 | `updateRelateOutBill` | 维修入库单 / 维修出库单 | 草稿保存阶段就会把维修出库明细挂上 `pushMaintainInBillDetailId`，不是审核后才挂接 | 待确认 | 确认业务上是否允许“仅建草稿也占用下推关系” |
| ⚠️ | 维修入库单 | `itemHandle` | 维修入库单 | 单件审核时把 `lockStatus` 直接改成未锁，反审时改成锁定；需要确认是否总能代表维修链真实锁语义 | 待确认 | 核对维修出库、维修详情对单件锁的约定 |
| ⚠️ | 维修入库单 | `billHandle` | 维修入库单 | 反审时单头改成 `2`，但明细 `billStatus` 仍被写成 `1`，头明细状态看起来不一致 | 待确认 | 确认这是有意保留“明细曾审核”标记，还是写错状态值 |
| ⚠️ | 维修入库单 | `preCheck` | 维修入库单 | 反审禁止条件依赖 `totalPushCount` 与“审核后是否又生成维修出库单”，后续若有别的下推链路要一起补校验 | 待确认 | 分析维修出库单和维修详情时继续核对后续占用来源 |
| ⚠️ | 维修出库单 | `updateRelateData` | 维修出库单 / 退厂登记池 | 草稿保存阶段就会把退厂登记单头改成 `OUTING`，不是审核后才推进 | 待确认 | 确认业务上是否允许“草稿即占用退厂池” |
| ⚠️ | 维修出库单 | `maintainOutNumHandle` | 维修出库单 / 维修单 | 审核时会把维修单明细上一轮入库结果整组清空，维护时很容易误伤 `newItem.../last...` 口径 | 待确认 | 后续改造必须把 `...Bak` 备份恢复链一并回归 |
| ⚠️ | 维修出库单 | `preCheck` | 维修出库单 | 反审强依赖 `totalInNum = 0` 与 `totalPushCount = 0`，只要后续已有维修入库或下推链就彻底锁死 | 待确认 | 分析维修详情时继续核对还有没有别的下游占用入口 |
| ⚠️ | 维修出库单 | `settleHandle` | 维修出库单 | 钱包口径是“材质重量 + 工费金额”一起扣减，若后续只改其中一边会造成供应商账不平 | 待确认 | 涉及钱包改动时必须同时回归材质钱包和现金钱包两条账本 |
| ⚠️ | 维修单详情 | `finalization` | 维修单详情 / 维修单 | 终结只拦“已完成”明细，没看到对真实流转完成度的前置校验，理论上可把未闭环明细直接终结 | 待确认 | 确认终结前是否应补 `replyStatus` 白名单和累计量校验 |
| ⚠️ | 维修单详情 | `finalization` | 维修单详情 | 终结时把 `outNum/inNum` 直接补成至少等于 `num`，可能掩盖真实未闭环数量 | 待确认 | 确认业务上是否允许“终结即补齐累计量” |
| ⚠️ | 维修单详情 | `updateStepLocal` | 维修单详情 | 以仓库名称 `"编货仓"` 文案判断并把 `replyStatus` 写成 `"6"`，属于脆弱文本耦合 | 待确认 | 确认是否应改成稳定仓库编码或枚举判断 |
| ⚠️ | 维修单详情 | `createItemEntryDetailVOByIds/createItemAdjustDetailVOByIds` | 维修单详情 / 单件链 | 下推单件入库和调整都依赖“最近一次维修入库明细”作为当前结果基础，若最近一笔缺失或滞后，会把旧结果继续下传 | 待确认 | 继续结合维修报表、单件入库和单件调整链核对来源可信度 |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalCustomer` | 维修单报表 | 控制器里把客户报表业务类型直接写死成“客户维修 + 展厅维修”，若后续新增客户侧维修类型，报表会天然漏数 | 待确认 | 确认这两个 `bizType` 是否就是客户报表的完整白名单 |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalDetail_from_where` | 维修单报表 | `replyType=3` 用 `step_ware_id = 457` 判断“在厂”，属于硬编码仓库 ID 口径 | 待确认 | 确认 `457` 是否稳定代表“厂修在途/在厂仓” |
| ⚠️ | 维修单报表 | `selectMaintainBillBiTotalDetailFillDataExpand` | 维修单报表 | 报表展示的“已回/未回/终结”是业务重算结果，不完全等于明细表 `replyStatus` 原值 | 待确认 | 联动前端确认页面文案是否明确“这是报表状态，不是单表原始状态” |
| ⚠️ | 维修单报表 | `maintainbillsettle` | 维修单报表 / 维修结算明细 | 结算明细模块名字容易被误判成“维修报表”，但它实际上只是 `wh_maintain_bill_settle` 的 CRUD/导出 | 待确认 | 总览和后续分析时明确把 `maintain-bill-bi` 视为维修报表主入口 |
| ⚠️ | 委外加工订单 | `calcSumGramWeight` | 委外加工订单 | `sumGramWeight` 当前按 `demandNum` 而不是 `outsourcingNum` 计算，重量口径可能失真 | 待确认 | 明确委外总重到底应按需求量还是委外量累计 |
| ⚠️ | 委外加工订单 | `discard` | 委外加工订单 | 只显式禁止 `CONFIRM`，未见对 `CANCELLATION/DELIVERED/FINISH` 的统一状态白名单 | 待确认 | 收敛可作废状态，避免已终态订单再次作废 |
| ⚠️ | 委外加工订单 | `updateProcessResult` | 委外加工订单 / BPM | BPM 回写当前只更新流程痕迹，不直接驱动委外单业务状态 | 待确认 | 确认流程审批是否只留痕，或补业务态联动 |
| ⚠️ | 维修结算明细 | `MaintainBillServiceImpl` 主流程 | 维修结算明细 / 维修单 | 维修单 VO 已带 `settleList`，但建单、改单、详情回显主流程未见稳定接线 | 待确认 | 补查 `settleList` 的保存、删除和回填链 |
| ⚠️ | 维修结算明细 | `deleteMaintainBillSettle` | 维修结算明细 | 当前只是物理删行，未见状态限制 | 待确认 | 确认是否仅允许未审核维修单删除结算行 |
| ⚠️ | 委外加工订单明细 | `deleteOutsourcingOrderDetail` | 委外加工订单明细 / 需求单 | 裸删明细，未见状态限制，也未回滚需求单累计 | 待确认 | 确认是否废弃该接口，或补回写保护 |
| ⚠️ | 委外加工订单明细 | `getOutsourcingSaveNum` | 委外加工订单明细 | 当前直接返回 `0`，看起来像未完成方法 | 待确认 | 确认是否已废弃，或补真实累计逻辑 |
| ⚠️ | 委外加工订单明细 | `getWaitEntryDetail` | 委外加工订单明细 | 已入库量按 `outsourcing_order_no` 聚合，不是按明细 ID 聚合 | 待确认 | 确认是否会把同单不同明细串口径 |
| ⚠️ | 结算方式 | `deleteSettlement` | 结算方式 / 各类结算明细 | 当前物理删除，未见“已被业务引用时禁止删除”的保护 | 待确认 | 增加引用校验或停用态替代删除 |
| ⚠️ | 结算方式 | `settleNo/settleMethod` 双口径 | 结算方式 / 业务规则 | 代码里既有按 `settleNo` 判别，也有按文案 `settleMethod` 判别，口径不统一 | 待确认 | 把业务识别统一收敛到稳定编码 |
| ⚠️ | 仓库结构 | `getDefaultArea` | 仓库 / 库区 | 兜底补建默认库区时，未明显回填 `wareId/deptId` | 待确认 | 核对默认库区补建后的字段完整性 |
| ⚠️ | 仓库结构 | 默认库位口径 | 仓库 / 库位 | 代码里同时出现 `A0`、`A01` 两套默认库位 | 待确认 | 统一默认库位命名口径 |
| ⚠️ | 仓库结构 | `getDefaultLocal` | 库位 | 无库位时插入 `A01` 后直接 `return null` | 待确认 | 修正为返回刚创建的默认库位 |
| ⚠️ | 客户档案 | `delete` | 客户档案 / 历史业务单据 | 当前只拦子客户和钱包，未见对需求单、销售、退货、维修等引用保护 | 待确认 | 增加历史业务引用校验 |
| ⚠️ | 客户档案 | `batchSubmit` | 客户档案 / BPM | 批量提审未见显式“仅顶级客户”限制 | 待确认 | 确认是否应与单提审保持一致 |
| ⚠️ | 供应商档案 | `deleteSupplier` | 供应商档案 / 财务账户 | 删除供应商未见同步处理绑定账户 | 待确认 | 确认账户应删除、停用还是保留 |
| ⚠️ | 供应商档案 | `updateSupplierStatus` | 供应商档案 / 钱包账户 | 停用只改供应商状态，未联动账户或钱包状态 | 待确认 | 明确停用的资金侧影响 |
| ⚠️ | 材质档案 | `deleteTexture` | 材质档案 / SKU库存业务链 | 物理删除，未见对 SKU、库存、钱包和历史业务引用保护 | 待确认 | 改为引用校验或停用态 |
| ⚠️ | 材质档案 | `isNumeric` | 材质档案 | 材质编号校验允许负数和小数 | 待确认 | 收紧为符合业务语义的编号规则 |
| ⚠️ | 大类档案 | `deleteBigClass` | 大类档案 / SKU业务链 | 物理删除，未见对 SKU、商品档案和历史业务引用保护 | 待确认 | 改为引用校验或停用态 |
| ⚠️ | 大类档案 | 建档校验 | 大类档案 | 目前只显式校验编号唯一，未见名称唯一保护 | 待确认 | 确认是否需要增加名称唯一校验 |
| ⚠️ | 品类结构 | 删品类/删子品类 | 品类 / 子品类 / 商城分类 | 未见对子结构、SKU、产品资料、商城分类树的清理或保护 | 待确认 | 梳理级联影响后补约束 |
| ⚠️ | 品类结构 | `getEnableList` | 子品类 | 名字是“启用列表”，但当前未显式按 `status` 过滤 | 待确认 | 确认是否应只返回启用数据 |
| ⚠️ | SKU 主档 | `deleteSku` | SKU / 库存单件BOM销售链 | 当前物理删除，未见库存、单件、BOM、销售等历史引用保护 | 待确认 | 改为引用校验或停用态 |
| ⚠️ | SKU 主档 | 审批通过后同步分支 | SKU / 商城标签 | 注释中的商城同步、标签同步分支当前未实际执行 | 待确认 | 确认这些分支是否已废弃或漏接 |
| ⚠️ | 供应商款式 / BOM | `deleteSupplierSku*` | 供应商 BOM | 头表和 accessories / part 清理链当前都是物理删除 | 待确认 | 增加默认供应商和历史引用保护 |
| ⚠️ | 供应商款式 / BOM | `getDirectListBySkuNos` | 供应商 BOM | 默认供应商识别依赖中文文案 `isDirect = "是"` | 待确认 | 改成稳定枚举或布尔值 |
| ⚠️ | SKU 规格 | `deleteSkuSpecs*` | SKU 规格 | 当前都是物理删除，未见对主档、缓存规格和历史查询的引用保护 | 待确认 | 增加引用保护 |
| ⚠️ | SKU 规格 | `updateSkuSpecs/deleteSkuSpecs` | SKU 规格 | 存在性校验被注释掉 | 待确认 | 恢复或重建校验逻辑 |
| ⚠️ | 新建款/改款 | `NewSkuBusiness/UpdateSkuBusiness` | SKU 审批域 | 聚合依赖极重，审批、导入、通知、缓存卫星和正式生效强耦合 | 待确认 | 改造时必须整链回归 |
| ⚠️ | 新建款/改款 | `FEE_FIELDS/...` | SKU 改款 | 改款同步强依赖硬编码费用字段集合 | 待确认 | 字段变更时补齐统一映射清单 |
| ⚠️ | SKU 审批明细宿主 | 明细宿主字段集 | 新建款明细 / 改款明细 | 明细宿主与正式 `sku` 主档字段容易漂移 | 待确认 | 建立字段对照与同步校验清单 |
| ⚠️ | SKU 审批域供应商 BOM 缓存 | 缓存 BOM 与正式 BOM | SKU 审批缓存 / 正式 BOM | 两边字段高度镜像，只改一边就会漂移 | 待确认 | 明确单向生效边界 |
| ⚠️ | SKU 审批域辅助链 | `synchronize` | 新建款 / 改款 / 正式 SKU | 同步动作会同时落正式 SKU、规格、BOM、图片和缓存清理，是高风险正式生效动作 | 待确认 | 涉及同步改造时做整链回归 |
| ⚠️ | SKU 规则主数据 | 设计元素空子类名单 | 设计元素 | “部分品类走空子类通用元素”是硬编码名单 | 待确认 | 建立可配置名单或补新增品类回归 |
| ⚠️ | SKU 规则主数据 | `processNo` | 生产工艺 | 当前只有 2 位编码格式，容量要确认 | 待确认 | 评估扩容策略 |
| ⚠️ | 系列与套系映射 | 删除链 | 系列 / 套系 / SKU | 系列、映射、套系当前都只看到物理删除 | 待确认 | 增加主档一致性和引用保护 |
| ⚠️ | 系列与套系映射 | `skuId1...skuId6` | 套系映射 | 套系结构最多只支持 6 个 SKU 槽位 | 待确认 | 确认是否满足业务上限 |
| ⚠️ | SKU 编码规则 | `genSkuRule` 调用方依赖 | SKU 编码规则 / SKU 主档 | 真正把 `serialNo` 落主表并写日志不在 `genSkuRule(...)` 内 | 待确认 | 梳理所有调用方是否都补全了落号动作 |
| ⚠️ | SKU 编码规则 | `serialNo` | SKU 编码规则 | 固定 4 位，容量需确认 | 待确认 | 评估扩位策略 |
| ⚠️ | 抄送配置 | `ccRecipientIds/transferIds` | 抄送配置 | 以逗号拼接字符串存用户 ID，没有关系表约束 | 待确认 | 确认是否需要拆关系表 |
| ⚠️ | 抄送配置 | 抄送人匹配 | 抄送配置 | 当前用字符串 `contains` 判断是否属于抄送人，存在子串误判风险 | 待确认 | 改为分隔后精确匹配 |
| ⚠️ | SKU 供应商库 | 删除链 | SKU 供应商索引 | 当前物理删除，未见历史引用保护 | 待确认 | 明确是否允许删历史归属关系 |
| ⚠️ | 客户侧 SKU 关系域 | `/delete` | 客户款式 | 控制器名义删除，实际只是停用 | 待确认 | 统一接口语义与真实动作 |
| ⚠️ | 客户侧 SKU 关系域 | `skucustomer/skucustomerlm` 冗余字段 | 客户 SKU 索引 / 老庙映射 | 都是创建时复制正式 SKU 字段，后续是否回刷不明确 | 待确认 | 梳理正式 SKU 变更后的刷新链 |
| ⚠️ | 款式模号 | `create/updateSkuModel` | 款式模号 | 重量段存在性判断看起来写反了，后面还会继续访问对象 | 待确认 | 先核代码再修正判断分支 |
| ⚠️ | 款式模号 | `fillData` | 款式模号 | 固定按 `PLAIN_GOLD` 回查重量段，不一定等于模号自身 `bigType` | 待确认 | 改成按模号自身大类判断 |
| ⚠️ | 共享库存 | `offShareStock` | 共享库存 | 成功后按 `customerId` 全量删共享库存，不是只删本次维度 | 待确认 | 收敛删除范围到本次共享维度 |
| ⚠️ | 共享库存 | `onShareStock/offShareStock` | 共享库存 | 捕获异常后只打日志不抛出，外部可能误判成功 | 待确认 | 明确失败返回语义 |
| ⚠️ | 共享方 | `setting` | 共享方 / 共享库存 | 先删后建，失败时旧绑定可能已被清空 | 待确认 | 改成事务内替换或先校验后写入 |
| ⚠️ | 共享方 | `setting/{id}` 权限 | 共享方 | 权限挂在 `query`，不是 `update` | 待确认 | 修正权限点 |
| ⚠️ | 共享流水与老庙接口痕迹 | `ShareFlowRecordServiceImpl.updateById` | 共享流水 | 返回值始终为 `null` | 待确认 | 修复返回语义，避免误导调用方 |
| ⚠️ | 共享流水与老庙接口痕迹 | `getOneBySerialNumber` | 老庙接口痕迹 | 多次重试时只 `limit 1`，未必命中最新痕迹 | 待确认 | 明确排序规则 |
| ⚠️ | 老庙订单与回调接口 | `wh:callback:lm` | 老庙接口层 | 两组接口共用一套权限，粒度偏粗 | 待确认 | 评估拆分权限点 |
| ⚠️ | 老庙订单与回调接口 | `pushYxysOrder/pushPPOrder` 异常分支 | 老庙回调 | 失败列表复用了同一个 `Map` 变量，存在串值风险 | 待确认 | 拆成每次循环新建对象 |
| ⚠️ | 接口同步任务 | `taskNo` | 同步任务 | 直接用时间戳字符串生成，极端并发下唯一性待确认 | 待确认 | 评估改成雪花号或库级唯一约束 |
| ⚠️ | 接口同步任务 | `/get-by-bizNo` | 同步任务 / 客户订单聚合 | 接口语义和真实数据来源不完全一致 | 待确认 | 明确命名或改回真实来源 |
| ⚠️ | 旧版共享库存链 | `wh_stock_share` vs `wh_share_stock` | 旧版共享 / 新版共享 | 新旧两套共享库存主表并存，容易形成双主数据漂移 | 待确认 | 明确当前主口径并标记迁移边界 |
| ⚠️ | 出货履约链 | `callbackYxysOrder/callbackPPOrder` | 出货单 / 客户订单 / 结算单 | 回调丢失会让履约长期停在中间态 | 待确认 | 梳理补偿或超时巡检机制 |
| ⚠️ | 出货履约链 | `LmStockSyncDelayQueue` | 出货单 / 共享库存 | 出货成功后仍挂着共享库存异步链，`ONGOING` 容易被误判为已完全成功 | 待确认 | 在状态口径里明确“仍有后续链路” |
| ⚠️ | 客户订单主链 | 多状态并存 | 客户订单 | `orderStatus/outOrderStatus/syncStatus/staffStatus/tellerSettleStatus` 五组状态并存 | 待确认 | 做状态矩阵，改造时按矩阵回归 |
| ⚠️ | 客户订单主链 | `createLmCustomerOrder` | 客户订单 | `shopId` 仍有默认 `1010L` 回退 | 待确认 | 确认默认网点口径是否仍有效 |



## 9. 补充覆盖汇总

### 9.1 已压缩纳入总览体系的模块簇
- 客户订单尾链：`customerorderreceive`、`customerorderkanban`、`customerlocation`、`customerorderitem`、`itemsale`、`ordermessage`
- 配件与辅石链：`accessories*`、`itemaccessories`、`entrybilldetailaccessories`、`supplierskuaccessories`、`supplierskupart`
- 资金与价格链：`account`、`wallet`、`walletdaily`、`walletcredit`、`shopbank`、`shopbankdaily`、`internalremittance`、`goldpricesetting`、`unitprice`、`unitpricemaintain`
- 组织与库存主数据：`shop`、`businessarea`、`businessareadept`、`businessstaff`、`measureunit`、`material`、`goodsstock`、`materialstock`、`itemhistorystock`、`skustock`
- 执行明细与卫星层：`entrybilldetail`、`materialinbounddetail`、`materialoutbounddetail`、`returndetail`、`goodsinboundbilldetail`、`goodsreject*detail`、`maintain*detail`、`cancelbilldetail`、`distributionbilldetail`、`shelvesbilldetail`
- 盘点与任务层：`inventorytask`、`printtask`、`printtaskdetail`、`inventoryweighingbillstock`、`iteminventorybillbatch`、`iteminventorybillitem`、`iteminventorybillstock`
- 半成品与客留尾链：`recycledetail`、`recyclebill`、`semistransfer`、`materialinventory`、`materialinventorydetail`、`materialinboundsettle`、`customerretain`
- 商品与 SKU 尾项：`categoryfour`、`categoryfive`、`categoryspec`、`bigweightsegment`、`skufavorite`、`skuseriesmapping`、`skusetmapping`、`skugemstone`、`skuparts`、`skushop`
- 客户与组织尾项：`customercontacts`、`customerstaffupdate`、`customerstaffupdatedetail`、`exhibition`、`qcstaff`
- 规则、日志与接口痕迹：`inrejectreason`、`skurulelog`、`skusearchlog`、`stockmessagerecord`、`shareflowrecord`、`lmapitransfer`

### 9.2 压缩后阅读规则
- `overview` 只保留主结构、图入口、执行进度和集中待办，不再重复追加“模块补充 + 关键结论 + 待办补充”三段式长尾块。
- 模块级结论以各自正文为准，入口统一从 2 节模块清单和 7 节图表索引进入。
- 全局风险和待确认项统一收敛到 8 节，不再在 8 节后重复堆叠补充块。
- 跨模块回写、字段语义和变更影响继续分别以 [cross-module-writeback.md](/D:/ws/code/wms-api/docs/business/cross-module-writeback.md)、[field-semantics.md](/D:/ws/code/wms-api/docs/business/field-semantics.md)、[change-impact.md](/D:/ws/code/wms-api/docs/business/change-impact.md) 为准。

### 9.3 当前收尾结论
- 按“不处理报表和查询壳”的口径，业务模块正文已经基本覆盖完成。
- 当前尚未单独展开的主要是支撑层：`bi`、`bizcache`、`changelog`、`common`、`excelCommonService`、`reportall`、`systemuserclient`。
- 后续如果继续补文档，建议只补支撑层技术说明，不再往 `overview` 追加流水式补充段。
