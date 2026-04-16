# 供应商出料单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
供应商出料单对应 `BillTypeEnum.MATERIAL_SUPPLIER_OUT`，与客户出料共表共服务，供应商侧有三种业务类型：
1. `2301 = M_DELIVER_MIX` 发料兑料
2. `2302 = M_SALE_RETURN` 发料提纯
3. `2303 = M_SALE_OUT` 卖料出库

它同样是“原料库存扣减 + 供应商钱包扣减”链，但不同业务类型决定到底扣材质、扣现金，还是两边都扣。

## 关键入口
- Controller: [MaterialOutboundSupplierController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/MaterialOutboundSupplierController.java)
- Service: [MaterialOutboundServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialoutbound/MaterialOutboundServiceImpl.java)
- 供应商分支工厂:
  - [OutboundSupplierFactory.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/factory/OutboundSupplierFactory.java)
  - [OutboundSupplierMix.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/bizbean/OutboundSupplierMix.java)
  - [OutboundSupplierPurify.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/bizbean/OutboundSupplierPurify.java)
  - [OutboundSupplierSale.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/materialoutbound/factory/bizbean/OutboundSupplierSale.java)

## 金额规则
创建时三种供应商业务类型的 `settleMoney` 口径不同：
1. `2301-发料兑料`
   - `settleMoney = 0`
2. `2302-发料提纯`
   - `settleMoney = originWeight * goldPrice`
3. `2303-卖料出库`
   - `settleMoney = convertWeight * goldPrice`

## 钱包工厂分支
### `2301-发料兑料`
`OutboundSupplierMix#doBizType()` 会移除现金钱包，只保留材质钱包。

业务含义：
- 只处理供应商欠料变化
- 不处理现金金额

### `2302-发料提纯`
`OutboundSupplierPurify#doBizType()` 不做过滤，材质钱包和现金钱包都保留。

业务含义：
- 同时处理欠料金重和欠款金额

### `2303-卖料出库`
`OutboundSupplierSale#doBizType()` 只保留现金钱包。

业务含义：
- 只处理供应商欠款变化
- 不处理材质钱包主账

## 审核与反审
供应商出料与客户出料共用同一套审核主链：
1. 审核：原料库存 `NEGATIVE`、钱包 `DEBT`
2. 反审：原料库存 `POSITIVE`、钱包 `DEPOSIT`

但跨日反审权限单独使用：
- `MATERIAL_OUTBOUND_SUPPLIER_CROSS_DAYS`

## 当前结论
1. 供应商出料单和客户出料单是“同表不同业务口径”，库存方向一样，钱包过滤规则不同。
2. `2301/2302/2303` 三支对应三种钱包形态：只动材质、材质+现金一起动、只动现金。
3. 发料兑料和卖料出库虽然都走出库链，但财务口径完全不同，不能混成一套“供应商出料”理解。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `confirm` | 供应商出料单 | 审核校验同样通过 `billName` 文案判断供应商分支，不是稳定的编码分支 | 待确认 | 确认是否应统一改成按 `billType/bizType` 判断 |
| ⚠️ | `doCreateMaterialOutboundBill` | 供应商出料单 | `2301/2302/2303` 的金额公式全部硬编码在同一方法里，后续改造容易串业务口径 | 待确认 | 变更前需按业务类型逐项回归 |
