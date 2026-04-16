# 成品入库单状态说明

## 文档定位
本文是成品入库单的阅读导航，帮助前后端开发、测试和联调快速理解入口动作、主状态和关键联动。

更细的业务规则、字段语义和跨模块回写，以以下文档为准：
- [docs/business/goodsinboundbill.md](/G:/wms_workspace/docs/business/goodsinboundbill.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/model/goods-inbound-bill-state.md](/G:/wms_workspace/docs/business/model/goods-inbound-bill-state.md)
- [docs/business/model/entity-goods-inbound-chain.md](/G:/wms_workspace/docs/business/model/entity-goods-inbound-chain.md)

如果本文与以上主资料有冲突，应优先回到 `docs/business/*` 校正。

## 前端可见动作
从 `wms-web` 的接口定义可以直接看到，成品入库单至少支持以下动作：

- 新建：`create`
- 修改：`update`
- 删除：`delete`
- 查询：`get / page / export-excel`
- 审核：`confirm`
- 反审：`infirm`
- 作废：`discard`
- 外部导入：`importFromU10`

## 主状态主线
结合接口命名和业务主资料，成品入库单主状态可按下面理解：

1. `INIT`
说明：草稿或未审核，允许修改、删除、补充明细。

2. `CONFIRM`
说明：审核通过，入库正式生效。

3. `INFIRM`
说明：已反审，已审核动作被撤销。

4. `DISCARD`
说明：单据终止，不再继续流转。

## 业务上需要特别注意的地方
成品入库单不是单纯“库存增加单”，而是一个复合业务入口。

- `0401` 是普通成品入库。
- `0402` 是供应商退料回流。
- `0403` 是转成品入库。
- 审核和反审不仅影响成品库存，还会联动供应商钱包。
- `0402/0403` 还会继续影响半成品回流记录、来源挂接和供应商领料台账。
- `importFromU10` 只覆盖普通成品入库口径，不等于覆盖全部业务类型。

## 联调时的最小核对清单
- 草稿态是否允许完整修改明细、附件和来源挂接。
- 审核后成品库存是否按业务类型正确增加。
- 审核后供应商钱包是否同步入账。
- 反审后库存、钱包、半成品回流量是否一起回滚。
- 作废是否只允许发生在未审核或已反审阶段。
- U10 导入单与手工建单在审核链路上是否遵循同一套状态约束。

## 与业务主资料的关系
本文只保留“状态导航 + 联调视角”。

- 要看 `0401/0402/0403` 的业务差异、金额重量口径、半成品挂接和来源引用，请看 [goodsinboundbill.md](/G:/wms_workspace/docs/business/goodsinboundbill.md)。
- 要看它在全局状态体系里的位置，请看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
- 要看更结构化的状态图和实体关系，请看 `docs/business/model` 下对应文件。
