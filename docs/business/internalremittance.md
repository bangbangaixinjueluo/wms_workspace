# 内部转账

## 模块定位
- 入口：`InternalRemittanceController`、`InternalRemittanceBusiness`、`InternalRemittanceServiceImpl`
- 宿主表：`wh_internal_remittance`、`wh_internal_remittance_detail`
- 这不是普通登记单，而是“网点银行卡内部调拨”业务宿主。

## 关键入口
- `/wh/internal-remittance/create`
- `/wh/internal-remittance/update`
- `/wh/internal-remittance/confirm/{id}`
- `/wh/internal-remittance/infirm/{id}`
- `/wh/internal-remittance/discard/{id}`
- `/wh/internal-remittance/page`

## 真实行为
- 建单时会按 `INTERNAL_REMITTANCE` 规则生成单号，明细必填。
- 每条明细都强校验：
  - 付款银行卡和收款银行卡不能相同
  - 转账金额不能为 0
- 单头 `totalRemitAmount` 会以后端重算结果为准。
- 改单只允许 `INIT/INFIRM`，且会先删旧明细再整批重建。
- 审核时会：
  - 把单头切到 `CONFIRM`
  - 遍历每条明细调用 `shopBankService.transferBalance(...)`
  - 生成 `ShopBankLogDO`
  - `bizType = INTERNAL_TRANSFER`
- 反审时要求当前单头处于已审，并且跨日反审还要检查财务/超管权限；随后以负金额做一次镜像 `transferBalance(...)` 回滚。
- 作废不允许针对已审核单据。

## 在主链中的作用
- 承接银行卡之间的内部调拨。
- 作为银行侧余额和银行流水的一条标准业务链。

## 关键结论
- 这是纯银行侧资金链，不走钱包。
- 审核/反审都是通过 `shopBankService.transferBalance(...)` 真正落账。

## 风险与待确认
- `⚠️` 改单链采用删旧明细后重建模式。
- `⚠️` `discard(id)` 只拦已审核单据，是否还应限制其他异常状态需要继续确认。
- `⚠️` 内部转账目前只看到银行卡余额与日志链，未见对更高层结算或日结任务的同步保护。
