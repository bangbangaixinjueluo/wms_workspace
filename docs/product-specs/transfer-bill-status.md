# 调拨单状态说明

## 文档定位
本文用于快速说明单件调拨链的主要状态、前端可见动作和联调关注点。

更完整的状态机和来源单据回写规则，以以下文档为准：
- [docs/business/transferbill.md](/G:/wms_workspace/docs/business/transferbill.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/model/transfer-state.md](/G:/wms_workspace/docs/business/model/transfer-state.md)
- [docs/business/model/entity-transfer-chain.md](/G:/wms_workspace/docs/business/model/entity-transfer-chain.md)

如果本文与以上主资料有冲突，应优先采用 `docs/business/*` 中的定义。

## 前端可见动作
从当前接口可以看到，调拨链至少包含以下动作：

- 新建、修改、删除、查询、分页、导出
- 审核：`confirm`
- 批量审核：`batchConfirm`
- 保存并审核：`saveAndConfirm`
- 接收：`received`
- 反审：`infirm`
- 作废：`discard`
- 驳回：`rejected`
- 退回中、退回接收、退回驳回：`returning / backReceived / backRejected`

## 主状态主线
结合接口和业务文档，调拨单的主状态不是简单的“草稿 -> 审核 -> 完成”，而是一条带回退分支的状态机：

1. `INIT`
说明：草稿态，通常允许修改和删除。

2. `CONFIRMING`
说明：部分场景下进入审批中，不代表已经完成接收。

3. `CONFIRM`
说明：审核通过，调拨动作成立，但目的仓未必已经签收。

4. `RECEIVED`
说明：目标仓或目标方完成接收。

5. `INFIRM`
说明：审核结果被撤销。

6. `DISCARD`
说明：单据终止。

7. `REJECTED / RETURNING / BACK_RECEIVED / BACK_REJECTED`
说明：用于处理拒收、退回、退回签收、退回驳回等逆向流程。

## 业务上需要特别注意的地方
- 调拨链至少要区分“审核通过”和“目标方已接收”，这两个状态不能混为一谈。
- 目标仓是否为展销仓，会影响是否进入 `CONFIRMING` 等审批型状态。
- 状态变化会回写来源单据的 `transferStatus`，不是只改单头状态。
- 调拨单和客留存、字印单、维修链等来源对象之间可能存在联动。
- 退回和驳回不是简单取消，而是有独立的逆向状态路径。

## 联调时的最小核对清单
- 审核后单件是否进入正确的在途或待接收口径。
- 接收后目标仓库存、库位和日志是否更新。
- 反审是否只允许发生在规定状态，例如 `CONFIRM` 或 `BACK_RECEIVED`。
- 驳回和退回是否正确恢复来源对象的调拨状态。
- 前端按钮可见条件是否和后端真实状态机保持一致。

## 与业务主资料的关系
本文只保留调拨状态导航，不覆盖所有逆向细节。

- 要看完整状态转移、方法入口和来源单据回写，请看 [transferbill.md](/G:/wms_workspace/docs/business/transferbill.md)。
- 要看它在全局状态矩阵中的位置，请看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
- 要看更原子化的状态与实体链说明，请看 `transfer-state.md` 和 `entity-transfer-chain.md`。
