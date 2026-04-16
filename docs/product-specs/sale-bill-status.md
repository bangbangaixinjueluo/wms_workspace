# 出库结算单状态说明

## 文档定位
本文用于帮助团队从产品和联调视角理解出库结算单的主状态。

更完整的实现细节、宿主关系和状态链，以以下文档为准：
- [docs/business/salebill.md](/G:/wms_workspace/docs/business/salebill.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/model/sale-bill-state.md](/G:/wms_workspace/docs/business/model/sale-bill-state.md)
- [docs/business/model/entity-settlement-chain.md](/G:/wms_workspace/docs/business/model/entity-settlement-chain.md)

如果本文与以上主资料有差异，应优先采用 `docs/business/*` 中的定义。

## 前端可见动作
从接口和页面结构可以看到，出库结算单连接了结算、发货、产品包和财务更新多个阶段：

- 新建、修改、删除、查询、分页、导出
- 审核：`confirm`
- 反审：`unConfirm`
- 作废：`obsolete`
- 保存并审核：`saveAndConfirm`
- 发货申请：`delivery-request`
- 可发货确认：`delivered`
- 实际发货：`actual-delivered`
- 不发货确认：`staff-no-delivery`
- 结算重做：`orderRedo`
- 调拨接收、拒收、退回、批量接收包
- 财务重算：`finance-update / finance-update-confirm`

## 主状态主线
根据业务主资料，结算单主状态应按下面这条链理解：

1. `INIT / INFIRM`
说明：草稿或反审后状态，允许继续编辑。

2. `CONFIRM`
说明：结算成立，客户钱包和产品包状态已联动更新。

3. `DELIVERY_ALLOW`
说明：已进入发货申请或待发货确认阶段。

4. `CONFIRM_ALLOW_DELIVERY`
说明：确认可发货。

5. `DELIVERED`
说明：实际发货已完成。

6. `CONFIRM_NO_DELIVERY`
说明：业务确认不发货，但结算仍成立。

7. `DISCARD`
说明：作废终止。

## 业务上需要特别注意的地方
出库结算单是核心宿主单据，不是普通 CRUD 单。

- 它会联动产品包状态。
- 它会联动客户订单明细本地状态。
- 它会联动客户留存。
- 它会联动客户钱包和结算明细。
- 真正的“发货完成”发生在 `actualDelivered`，不是 `confirm`。
- `orderRedo` 会把已发货或不发货状态重新拉回已审核状态，是一条正式的回滚链。

## 联调时的最小核对清单
- 审核后产品包是否进入正确状态。
- 审核后客户订单明细是否从结算中推进到已结算或已开票口径。
- 发货申请、可发货确认、实际发货这三步是否被前端误合并。
- 实际发货后库存扣减、客户留存、出货单生成是否都发生。
- 重做后库存、客户留存、订单出货信息是否都回滚。
- 财务更新链是否只是重算，还是会重走整条审核到发货流程。

## 与业务主资料的关系
本文保留的是“状态主线和联调口径”，不是完整实现说明。

- 要看完整动作入口、状态流和上下游联动，请看 [salebill.md](/G:/wms_workspace/docs/business/salebill.md)。
- 要看它在整体状态体系中的位置，请看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
- 要看更细的状态定义和结算实体链，请看 `sale-bill-state.md` 与 `entity-settlement-chain.md`。
