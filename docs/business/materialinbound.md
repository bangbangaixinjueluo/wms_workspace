# 客户来料单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
客户来料单对应 `BillTypeEnum.MATERIAL_CUSTOMER_IN`，业务类型是 `BizTypeEnum.M_CUSTOMER_IN(2001)`。它不是单纯的原料入库单，而是把客户来料、来料结算、客户欠料/欠款钱包一起落账的复合单据。

审核时会同时做三件事：
1. 按来料明细增加原料库存，并写 `wh_material_stock_log`
2. 按结算明细回写客户材质钱包
3. 按单头 `totalSettleMoney` 反向回写客户现金钱包，并对 `CLOSING_PRICE(02)` 额外做一笔材质钱包冲回

反审时三条链整体反向回滚。

## 关键入口
- Controller: [MaterialInboundController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialinbound/MaterialInboundController.java)
- Service: [MaterialInboundServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinbound/MaterialInboundServiceImpl.java)
- 结算明细服务: [MaterialInboundSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinboundsettle/MaterialInboundSettleServiceImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_material_inbound` | 主表 | 来料单头，记录客户/账户、欠料欠款快照、合计字段 |
| `wh_material_inbound_detail` | 明细表 | 来料材质、来料重、折算重、提纯费、结算金额等 |
| `wh_material_inbound_settle` | 结算表 | 转欠 / 结价等结算方式明细 |
| `wh_material_stock` | 库存表 | 审核/反审时增减原料库存 |
| `wh_material_stock_log` | 库存日志 | 记录原料库存入账/冲回痕迹 |
| `wh_wallet` | 钱包 | 记录客户现金钱包、材质钱包余额 |
| `wh_wallet_log` | 钱包日志 | 记录客户钱包过账痕迹 |

## 单据主链
### 创建
`doCreateMaterialInboundBill()` 会先跑 `validateMaterialInboundBill()` 和 `validateDetailAndSettleWallet()`，然后一次性写单头、明细、结算。

核心规则：
1. 客户来料必须有结算明细，否则抛 `MATERIAL_INBOUND_SETTLE_NOT_EXISTS`
2. 明细必须有来料数据，且 `originWeight >= 0`
3. 所有明细 `walletTypeNo` 必须一致，结算明细里的材质钱包类型也必须和明细一致
4. 若单头 `remark` 为空，则“明细折算重合计”必须等于“结算重量合计”；有备注时才允许不相等

### 单头重算
创建时会重算而不是直接信前端：
1. 明细 `convertWeight = originWeight * convertRate`
2. 但 `materialNo == "06"` 时，`convertWeight` 直接取来料重四舍五入到 2 位
3. 客户来料明细 `settleMoney = originWeight * purifyUnitFeeTaxed`
4. 汇总 `totalOriginWeight / totalConvertWeight / totalSettleWeight / totalPurifyFee / totalSettleMoney`
5. 读取当前账户钱包余额，回填 `stockWeightNow / stockMoneyNow`
6. `totalStockWeight` 会被保存成负数：`总转欠重量 * -1`

`totalStockWeight` 不是“本次入库重量”，而是“本单导致的欠料变化量”。

### 审核
`confirm()` 的顺序是：
1. 校验每条明细 `originWeight > 0`
2. 校验“来料重不为 0 时，折算重不能为 0”
3. 校验结算明细里 `settleType == "1"` 的项目 `goldPrice != 0`
4. `BillStatusEnum.confirmCheck(...)`
5. 单头状态改成 `CONFIRM`
6. `doMaterialStock(..., POSITIVE)` 增加原料库存
7. `doUpdateWalletAndWriteLog(..., DEPOSIT, CONFIRM)` 回写钱包

### 反审
`unConfirm()` 的顺序是：
1. `BillStatusEnum.infirmCheck(...)`
2. 跨日反审需要权限 `CommonConstant.MATERIAL_INBOUND_CROSS_DAYS`
3. 单头状态改成 `INFIRM`
4. `doMaterialStock(..., NEGATIVE)` 冲回原料库存
5. `doUpdateWalletAndWriteLog(..., DEBT, INFIRM)` 冲回钱包

## 钱包链路
### 材质钱包主账
客户来料的钱包主账不是按明细直接汇总，而是按结算明细汇总：
- `getNotCloseWalletData(...)`
- 取所有 `materialNo` 非空的结算行
- 以 `materialNo + settleClass` 聚合 `settleWeight`

这意味着审核时真正落客户材质钱包的是“结算重量”，不是简单的“明细折算重”。

### 现金钱包
客户来料还有一笔现金钱包动作：
- 审核时：`cashAccountingType = DEBT`
- 反审时：`cashAccountingType = DEPOSIT`
- 金额取单头 `totalSettleMoney`

### 结价分支
客户来料如果带 `CLOSING_PRICE(02)`，还会额外调用 `getCloseWalletData(...)` 再做一次材质钱包动作。

它延续结价模块的统一口径：
1. 先把结算重量并进主账
2. 再把结价对应的重量单独按相反方向冲回

## 库存链路
客户来料审核/反审始终会改原料库存：
1. `detailsService.computeForStock(details)` 汇总材质重量
2. 组装 `MaterialStockLogDO`
3. `logType = ItemLogTypeEnum.IN`
4. 审核传 `StockAccountingTypeEnum.POSITIVE`
5. 反审传 `StockAccountingTypeEnum.NEGATIVE`

## 更新、删除、作废
### 更新
`updateMaterialInbound()` 只允许 `INIT/INFIRM`，并且会先删老明细、老结算，再把更新请求转成创建请求重走一遍建单逻辑。

### 删除
`deleteMaterialInbound()` 会级联删除明细和结算，再删主表。

### 作废
`obsolete()` 通过 `BillStatusEnum.obsoleteCheck(...)` 控制，然后把单头状态改成 `DISCARD`。这一分支没有库存/钱包回滚逻辑，因此只能理解为未审核单据作废。

## 当前结论
1. 客户来料单是“原料库存 + 材质钱包 + 现金钱包 + 结价冲回”的复合链路。
2. 审核/反审时，真正回写客户材质钱包的依据是结算明细 `settleWeight`，不是明细 `convertWeight` 直接落账。
3. `totalStockWeight` 是欠料变化量，保存时故意取负，不能按普通“正向入库重量”去理解。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `getCloseWalletData` | 客户来料单 / 结价 | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定的 `settleNo` 或 `settleType` | 待确认 | 确认是否应改成按 `settleNo=02` 识别 |
| ⚠️ | `confirm` | 客户来料单 | 审核校验报错文案混有“出料重量/来料重量”历史措辞，代码语义偏混乱 | 待确认 | 确认是否只是文案遗留 |
| ⚠️ | `doCreateMaterialInboundBill` | 客户来料单 | `totalStockWeight` 建单时被直接取负，若后续报表按“本次来料重”理解，容易看反 | 待确认 | 确认前端/报表是否已按欠料变化量解释 |
| ⚠️ | `wh_material_inbound.process_result/process_instance_id` | 客户来料单 / BPM | 主表保留了 BPM 字段，但当前服务实现里未见对应流程回写链 | 待确认 | 继续确认审批流是否迁移到别处或已废弃 |
