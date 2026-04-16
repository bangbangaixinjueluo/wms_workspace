# 客户退货单状态说明

## 文档定位
本文用于从业务导航视角说明客户退货单的主状态、前端动作和联调关注点。

退货链的完整业务定义，以以下文档为准：
- [docs/business/returnbill.md](/G:/wms_workspace/docs/business/returnbill.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/model/return-bill-state.md](/G:/wms_workspace/docs/business/model/return-bill-state.md)
- [docs/business/model/entity-return-chain.md](/G:/wms_workspace/docs/business/model/entity-return-chain.md)

如果本文与以上主资料不一致，应优先回到 `docs/business/*` 校正。

## 前端可见动作
当前接口至少暴露了以下退货动作：

- 新建、修改、删除、查询、分页、导出
- 审核：`confirm`
- 反审：`unConfirm`
- 作废：`obsolete`
- 按销售单分页关联：`salePage`
- 包装或单件明细关联查询
- 退货结算明细查询与导出

## 主状态主线
客户退货单的主状态可以先按下面这条线理解：

1. `INIT`
说明：草稿或未审核状态。

2. `CONFIRM`
说明：退货正式生效。

3. `INFIRM`
说明：已反审，退货动作被撤销。

4. `DISCARD`
说明：作废终止。

## 业务上需要特别注意的地方
客户退货单不是“商品回库”这么简单，而是一个复合回写入口。

- 会把单件或成品库存回库。
- 会回写客户钱包。
- 会处理结价分支的反向冲回。
- 可能联动维修流转仓位。
- 单据同时兼容 EPC 单件退货和非 EPC 成品退货，两类明细共存于一张单内。
- 单头金额和重量口径主要来自退货结算明细，而不是直接来自商品明细求和。

## 联调时的最小核对清单
- 退货是否必须关联销售单、包装或单件来源。
- 审核后库存是否按 EPC 与非 EPC 两条链分别正确回库。
- 审核后客户钱包和结价冲回是否同步落账。
- 反审后库存、钱包和维修流转仓是否一起恢复。
- 作废是否只允许发生在未审核或已反审场景。

## 与业务主资料的关系
本文只承担“退货状态入口导航”的职责。

- 要看 EPC 与非 EPC 的差异、钱包处理、结价冲回和维修联动，请看 [returnbill.md](/G:/wms_workspace/docs/business/returnbill.md)。
- 要看退货在全局状态矩阵中的位置，请看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
- 要看更细的状态和实体链说明，请看 `return-bill-state.md` 与 `entity-return-chain.md`。
