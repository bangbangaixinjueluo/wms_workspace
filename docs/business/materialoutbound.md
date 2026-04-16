# 客户出料单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
客户出料单对应 `BillTypeEnum.MATERIAL_CUSTOMER_OUT`，共用 `wh_material_outbound / wh_material_outbound_detail` 主表和服务实现，客户侧主要有两种业务类型：
1. `2101 = M_CUSTOMER_BUY` 客户买料
2. `2102 = M_CUSTOMER_RETURN` 退客户料

它不是单纯原料出库单，而是“原料库存扣减 + 客户钱包扣减”复合链。审核时同时扣库存、扣钱包；反审时同时加回。

## 关键入口
- Controller: [MaterialOutboundController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/MaterialOutboundController.java)
- Service: [MaterialOutboundServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialoutbound/MaterialOutboundServiceImpl.java)
- 客户分支工厂:
  - [OutboundCustomerFactory.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/factory/OutboundCustomerFactory.java)
  - [OutboundCustomerBuy.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/bizbean/OutboundCustomerBuy.java)
  - [OutboundCustomerReturn.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/bizbean/OutboundCustomerReturn.java)

## 主链
### 创建
`doCreateMaterialOutboundBill()` 会重算并回填：
1. 明细 `convertWeight`
2. 明细 `settleMoney`
3. 单头 `totalOriginWeight / totalConvertWeight / totalSettleWeight / totalSettleMoney`
4. 单头 `stockWeightNow / stockMoneyNow`

与来料链一致：
- `materialNo == "06"` 时，`convertWeight` 直接取来料金重四舍五入到 2 位
- 其余材质按 `originWeight * convertRate`

### 金额公式
客户侧两种业务类型的钱包金额口径不同：
1. `2101-客户买料`
   - `settleMoney = convertWeight * goldPrice`，并四舍五入到整数
2. `2102-退客户料`
   - 走客户工厂分支时会过滤掉现金钱包，只保留材质钱包

因此客户买料本质上是“动现金不动金重”，退客户料本质上是“动金重不动现金”。

### 审核
`confirm()` 的顺序是：
1. 校验明细 `originWeight > 0`
2. 校验“出料重不为 0 时，折算重不能为 0”
3. `BillStatusEnum.confirmCheck(...)`
4. 单头状态改成 `CONFIRM`
5. `doMaterialStock(..., NEGATIVE)` 扣减原料库存
6. `doUpdateWalletAndWriteLog(..., DEBT)` 扣减客户钱包

### 反审
`infirm()` 的顺序是：
1. `BillStatusEnum.infirmCheck(...)`
2. 跨日反审需要权限 `MATERIAL_OUTBOUND_CROSS_DAYS`
3. 单头状态改成 `INFIRM`
4. `doMaterialStock(..., POSITIVE)` 回补原料库存
5. `doUpdateWalletAndWriteLog(..., DEPOSIT)` 回补客户钱包

## 钱包链
`doUpdateWalletAndWriteLog()` 会先把明细汇总成：
1. 材质重量映射
2. 再额外塞入现金钱包编码，金额取 `totalSettleMoney`

随后交给客户分支工厂过滤：

### `2101-客户买料`
`OutboundCustomerBuy#doBizType()` 只保留现金钱包。

业务含义：
- 客户把钱付出来买料
- 钱包侧只记现金欠款变化
- 原料金重不进客户钱包主账

### `2102-退客户料`
`OutboundCustomerReturn#doBizType()` 会移除现金钱包，只保留材质钱包。

业务含义：
- 系统把料退给客户
- 钱包侧只记材质欠料变化
- 不处理现金金额

## 库存链
客户出料审核/反审始终会改原料库存：
1. 审核传 `StockAccountingTypeEnum.NEGATIVE`
2. 反审传 `StockAccountingTypeEnum.POSITIVE`
3. `MaterialStockLogDO.logType = OUT`

因此它在库存语义上是标准原料出库链。

## 更新、删除、作废
1. 更新只允许 `INIT/INFIRM`，并且会先删老明细，再重走创建逻辑
2. 删除会级联删除明细，再删主表
3. 作废走 `BillStatusEnum.obsoleteCheck(...)`，只改状态，不做库存/钱包回滚

## 当前结论
1. 客户出料单是“原料库存扣减 + 客户钱包扣减”复合链。
2. `2101` 和 `2102` 的关键差别不在库存，而在钱包过滤规则：前者只动现金，后者只动材质。
3. 审核/反审方向与客户来料正好镜像：来料是库存加、钱包加；出料是库存减、钱包减。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `confirm` | 客户出料单 | 审核校验仍通过 `billName` 文案分支判断客户/供应商，不是稳定的 `billType/bizType` | 待确认 | 确认是否存在文案变更导致分支失效的风险 |
| ⚠️ | `discard` | 客户出料单 | 作废只改状态，不做库存/钱包回滚，因此必须依赖状态机保证未审核单才可作废 | 待确认 | 核对 `BillStatusEnum.obsoleteCheck()` 精确可作废状态 |
