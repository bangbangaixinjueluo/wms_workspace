# 供应商来料单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
供应商来料单对应 `BillTypeEnum.MATERIAL_SUPPLIER_IN`，共用客户来料同一套主表、明细表、结算表和服务实现，但业务类型拆成两支：
1. `BizTypeEnum.M_PURCHASE_IN(2201)` 采购入库
2. `BizTypeEnum.M_PURIFICATION_IN(2202)` 提纯入库

它的本质是“原料入库 + 供应商钱包过账”链，不走银行卡，也不像客户来料那样依赖结算明细来组织钱包主账。

## 关键入口
- Controller: [MaterialInboundSupplierController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialinbound/MaterialInboundSupplierController.java)
- Service: [MaterialInboundServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialinbound/MaterialInboundServiceImpl.java)
- 钱包工厂:
  - [InboundSupplierFactory.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialinbound/factory/factory/InboundSupplierFactory.java)
  - [InboundSupplierBuy.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialinbound/factory/bizbean/InboundSupplierBuy.java)
  - [InboundSupplierPurify.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialinbound/factory/bizbean/InboundSupplierPurify.java)

## 与客户来料的共性
1. 共用 `wh_material_inbound / detail / settle`
2. 审核都会 `doMaterialStock(..., POSITIVE)` 增加原料库存
3. 反审都会 `doMaterialStock(..., NEGATIVE)` 冲回原料库存
4. 更新都只允许 `INIT/INFIRM`
5. 删除都会级联删明细、结算，再删主表
6. 作废也都是直接把状态改成 `DISCARD`

## 供应商分支的差异
### 钱包主账来源
供应商来料不走客户专用的 `getNotCloseWalletData(settles)`，而是：
1. 先按明细 `detailsService.computeForWallet(details)` 算出材质/金额映射
2. 额外把 `totalPurifyFee` 填进现金钱包编码
3. 再交给工厂分支 `doBizType(...)` 过滤真正要动的钱包类型
4. 最后 `walletService.getWalletData(..., SETTLE_CLASS_MATERIAL)` 生成钱包分录

### 采购入库
`InboundSupplierBuy#doBizType(...)` 的规则是：
1. 若现金金额 `cash > 0`，只保留现金钱包
2. 若现金金额 `cash <= 0`，移除现金钱包，只保留材质钱包

这段逻辑的业务语义是：
- 有金额时，来料被理解成欠款变化
- 没金额时，来料被理解成欠料变化

### 提纯入库
`InboundSupplierPurify#doBizType(...)` 不做过滤，材质钱包和现金钱包都保留。

### 金价校验
供应商来料在钱包过账前会再次校验：`settleType == "1"` 时 `goldPrice > 200`。

### 反审权限
供应商采购入库、提纯入库共用另一组跨日权限：
- `CommonConstant.MATERIAL_INBOUND_SUPPLIER_CROSS_DAYS`

## 当前结论
1. 供应商来料单和客户来料单是“同表不同业务口径”，不能因为主表一样就当成同一个财务链。
2. 客户来料的钱包主账吃的是结算明细；供应商来料的钱包主账吃的是明细汇总再经工厂分支过滤。
3. 采购入库与提纯入库的关键差别不在库存，而在钱包过滤规则：采购入库会在现金/材质之间二选一，提纯入库则可能两边都动。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `InboundSupplierBuy#doBizType` | 供应商来料单 | 采购入库用“现金是否大于 0”决定只走现金还是只走材质钱包，属于较强的隐式业务规则 | 待确认 | 确认业务上是否始终接受“二选一” |
| ⚠️ | `doUpdateWalletAndWriteLog` | 供应商来料单 | 钱包过账前对 `settleType == 1` 又校验一次 `goldPrice > 200`，与客户来料口径不同 | 待确认 | 确认该门槛是否只适用于供应商采购入库 |
| ⚠️ | `obsolete` | 供应商来料单 | 作废只改状态，不做库存/钱包回滚，因此必须依赖状态机保证未审核单才可作废 | 待确认 | 核对 `BillStatusEnum.obsoleteCheck()` 的精确可作废状态 |
