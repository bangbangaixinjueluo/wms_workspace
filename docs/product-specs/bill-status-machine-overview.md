# 单据状态流转总览

## 目的
本文汇总当前项目中几类核心单据的状态流转特征，作为更细分单据文档的导航页。

## 说明
本文依据三类信息整理：

- 前端接口动作名
- 历史 README 中的单据状态说明
- 页面与目录命名
- `docs/business` 下已整理的业务逻辑与状态资料

其中：
- “创建 / 更新 / 删除 / 查询 / 导出”属于明确能力
- “确认 / 反审 / 作废 / 接收 / 退回 / 驳回”属于明确状态动作
- 更细业务含义若未直接体现在代码中，则只做保守描述

如果后续要把本文提升为更精确的状态机，应优先对照：

- `docs/business/state-matrix.md`
- `docs/business/model/*-state.md`

## 当前已整理单据
- [goods-inbound-bill-status.md](./goods-inbound-bill-status.md)
- [transfer-bill-status.md](./transfer-bill-status.md)
- [sale-bill-status.md](./sale-bill-status.md)
- [return-bill-status.md](./return-bill-status.md)
- [counting-bill-status.md](./counting-bill-status.md)

## 从历史文档可确认的通用状态口径
旧文档里出现过以下通用状态：

- `0`：未审
- `1`：审核中
- `2`：已审
- `3`：反审
- `4`：作废
- `5`：已接收 / 已退厂 / 已开单等业务特化终态

这说明项目里很多单据共用“草稿/待审/已审/反审/作废/接收类终态”的基础状态模型。

## 共同风险点
- 状态名称相似，但不同单据的业务语义不一定相同
- 接收、驳回、退回等动作常常意味着跨仓、跨方或跨阶段流转
- 是否允许反审、是否允许作废、是否允许回退，需要逐单据验证

## 联调建议
- 单据联调不要只测“创建成功”
- 至少覆盖：创建、确认、反审、作废、接收/回退这类状态边界
- 同步验证库存、财务和报表是否随状态变化而变化
