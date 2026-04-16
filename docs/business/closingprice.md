# 结价单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
“结价单”在当前代码里不是独立 controller + 主表单据，而是一个跨模块的结算分支：以 `CLOSING_PRICE(02)` 结算方式嵌入销售结算、客户退货、维修确认等业务链。它的核心作用不是单独立单，而是在“转欠/存欠”基础上，对指定材质和类别的结算重量再做一笔方向相反的结价冲回。

## 关键定位
- 结价编码：`SaleSettleNoEnum.CLOSING_PRICE = "02"`
- 同组转欠编码：`SaleSettleNoEnum.RECIRCULATION = "01"`
- 业务归属：
  - 销售结算：`SettlementBizTypeEnum.SETTLEMENT_SALE("01")`
  - 销售退货：`SettlementBizTypeEnum.SETTLEMENT_RETURN("02")`
  - 客商来料：`SettlementBizTypeEnum.SETTLEMENT_IN("03")`
- 结价方式主数据落在 `wh_settlement`
- 业务明细依附在：
  - `wh_sale_settle`
  - `wh_return_settle`
  - `wh_material_inbound_settle`

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|------|------|
| `wh_settlement` | 读 | 结算方式主数据，`02=结价` 只是其中一种结算方式 |
| `wh_sale_settle` | 读写 | 出库结算单的结算明细，保存 `settleNo/settleType/materialNo/settleWeight/settleMoney/goldPrice` |
| `wh_return_settle` | 读写 | 客户退货单的结算明细 |
| `wh_material_inbound_settle` | 读写 | 客商来料单的结算明细 |
| `wh_wallet` / `wh_wallet_log` / `wh_wallet_daily` | 读写 | 真正的钱包变动落点，结价以材质钱包分录形式体现 |

## 入口与依附关系

### 销售结算链
- 结价明细创建/修改：`SaleBillBusiness.handlerSaleBillSettle()`
- 审核校验：`SaleBillBusiness.confirm()`
- 钱包动作：
  - `buildNotCloseWalletData()`：转欠总账，包含结价重量
  - `buildCloseWalletData()`：把结价部分再单独反向转一次

### 客户退货链
- 结价明细读取：`ReturnSettleServiceImpl`
- 审核校验：`ReturnBillBusiness.confirm()`
- 钱包动作：
  - `handlerNotCloseWallet()`：先按存账/转欠主链处理
  - `handlerCloseWallet()`：把结价部分再反向处理

### 维修确认链
- 维修确认时直接调用：
  - `createWalletBaseDTO(WalletAccountingTypeEnum.DEBT)`：包含结价在内的存欠处理
  - `createCloseWalletBaseDTO(WalletAccountingTypeEnum.DEPOSIT)`：把结价部分单独冲回

## 业务规则

### 结价不是脱离主单据独立流转
1. 没有单独 `wh_closing_price_bill` 这类主表。
2. 结价状态完全依附宿主单据：
   - 销售结算时依附出库结算单
   - 退货结价时依附客户退货单
   - 维修结价时依附维修确认链
3. 因此不能把它按“普通财务单据状态机”理解。

### 结价必须带材质和类别
1. 在销售结算链，`settleNo=02` 时强制要求：
   - `materialNo` 非空
   - `settleClass` 非空且必须是材质/配件类
2. 审核时 `settleType = 1` 的结价项还要求 `goldPrice != 0`。

### 结价的钱包语义是“先并入转欠，再单独反向冲回”
1. 销售结算审核：
   - 先按 `DEBT` 记一笔“包含结价重量”的转欠/存欠主账
   - 再按 `DEPOSIT` 把 `CLOSING_PRICE` 对应重量单独冲回
2. 客户退货审核：
   - 先按 `DEPOSIT` 记主账
   - 再按 `DEBT` 把结价部分单独冲回
3. 维修确认审核：
   - 也是“主账一笔 + 结价反向一笔”的双动作

### 结价改变的是材质钱包，不是银行卡
1. 结价分支始终围绕 `materialNo + settleClass` 聚合。
2. 现金金额主账可能随宿主单据一起进入钱包，但“结价”本身的关键动作是金重材质账反向分录。

## 当前结论
- “结价单”在业务上是独立概念，但在实现上是“结算明细中的一种结算方式”，不是独立表单据。
- 真正的结价逻辑不在 `SettlementController`，而在宿主业务单据审核时对 `CLOSING_PRICE(02)` 的特殊处理。
- 如果后续改“结价”，必须同时看“主账处理”和“结价反向冲回”两笔钱包动作；只改一边一定会导致材质欠账失衡。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `SaleBillBusiness.buildNotCloseWalletData` | 结价单 / 销售结算 | 注释明确写着“转欠中如果有结价，结价重量先合并进转欠，再再次转欠”，属于双重处理口径，稍不注意就会误改 | 待确认 | 后续改造前必须先核对业务是否仍要求“主账包含结价 + 结价反向一笔” |
| ⚠️ | `ReturnBillBusiness.handlerCloseWallet` | 结价单 / 客户退货 | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是只靠 `settleNo/settleType`，有名称口径耦合 | 待确认 | 确认是否应统一改成 `settleNo` 或 `settleType` 驱动 |
| ⚠️ | `SettlementController` | 结价单 | `settlement` 模块只是结算方式主数据，若把它误当成“结价单”主业务会看错链路 | 已确认 | 总览里应把“结价单”与“结算方式”明确区分 |
