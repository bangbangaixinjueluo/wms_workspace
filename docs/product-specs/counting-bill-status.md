# 单件盘点单状态说明

## 文档定位
本文用于帮助开发、测试和实施同学快速理解单件盘点单的状态和使用边界。

盘点模块的更完整分析，以以下文档为准：
- [docs/business/iteminventorybill.md](/G:/wms_workspace/docs/business/iteminventorybill.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/model/item-inventory-bill-state.md](/G:/wms_workspace/docs/business/model/item-inventory-bill-state.md)
- [docs/business/model/entity-item-inventory-chain.md](/G:/wms_workspace/docs/business/model/entity-item-inventory-chain.md)

如果本文与以上主资料存在差异，应优先采用 `docs/business/*`。

## 前端可见动作
从接口可以看出，单件盘点并不是普通单据录入，而是“批次采集式”处理：

- 创建盘点批次：`createBatchData`
- 修改盘点批次：`updateBatchData`
- 创建盘点单：`createInventoryBill`
- 修改盘点单：`updateInventoryBill`
- 查询批次、账单、分页、待盘项目
- 审核通过：`approved`
- 反审核：`returnApproved`
- 作废：`discard`
- 批量作废：`discardBatch`
- 差异页和差异导出

## 主状态主线
结合业务主资料，单件盘点单应先按下面这条主线理解：

1. `INIT`
说明：盘点单已创建，基线快照已冻结。

2. `IN_PROGRESS`
说明：实际业务上处于批次采集和差异比对阶段。

3. `CONFIRM`
说明：审核通过，单头状态确认完成。

4. `INFIRM`
说明：反审核，撤销单头审核状态。

5. `DISCARD`
说明：作废终止。

## 业务上需要特别注意的地方
- 单件盘点单本质是“盘点基线快照 + 批次采集 + 差异查询”模块。
- 审核和反审核当前主要体现为单头状态变化，不等于正式库存过账。
- 盘点盈亏更接近“当前盘点视角下的额外 EPC 或差异结果”，不是直接等价于库存主表已经被修正。
- 批次作废是撤销某次采集结果，不等于删除整张盘点单。
- 前端盘点列表可能混合展示单件盘点和盘点称重，不应直接把页面结果等同于单一表结构。

## 联调时的最小核对清单
- 建单时是否已经冻结当时的盘点基线。
- 批次采集是否会区分“账内已存在但被盘到”和“额外发现的 EPC”。
- 审核后是否真的没有直接改 `wh_item`、`wh_sku_stock` 等库存主口径。
- 反审核是否只撤销单头审核状态，而不回滚实际库存。
- 批次作废是否只影响对应批次采集结果和聚合口径。

## 与业务主资料的关系
本文只负责说明盘点单的状态导航和使用边界。

- 要看建单初始化、批次采集、盘盈口径和作废行为，请看 [iteminventorybill.md](/G:/wms_workspace/docs/business/iteminventorybill.md)。
- 要看它在整体状态矩阵中的位置，请看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
- 要看更细的状态和实体链说明，请看 `item-inventory-bill-state.md` 与 `entity-item-inventory-chain.md`。
