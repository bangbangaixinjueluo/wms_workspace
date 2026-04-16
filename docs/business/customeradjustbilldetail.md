# 客户应收调整明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`customeradjustbilldetail` 是客户应收调整单的事实明细层和权限筛选层。主单负责审核改钱包，明细层负责行级事实、分页和客户权限过滤。

## 关键入口
- Service: [CustomerAdjustBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/customeradjustbilldetail/CustomerAdjustBillDetailServiceImpl.java)

## 真实行为
- `getCustomerAdjustBillDetailVOPage(...)`
  - 支持先按 `customerNameAux` 反查客户，再分页查明细。
- `listAuthByRole(...)`
  - 按登录人角色收缩客户可见范围，业务员链会只看自己名下客户。
- `getPage(...)`
  - 走自定义 mapper 分页，承接客户编号扩展筛选。
- `selectListByBillId(id)`
  - 主单回读整组明细。

## 在主链中的作用
- `customeradjustbill`
  - 主单审核/反审改钱包；明细层承接每笔应收调整事实和可见性。

## 风险与待确认
1. `⚠️` `deleteCustomerAdjustBillDetail(...)` 当前物理删除，未见主单状态保护。
2. `⚠️` `listAuthByRole(...)` 中 `wareIds` 虽然计算了，但当前没有继续参与过滤，权限口径需要确认。
3. `⚠️` `customerNameAux` 查不到时会把 `customerId` 置成 `0L`，依赖 mapper 把它当空结果处理。

