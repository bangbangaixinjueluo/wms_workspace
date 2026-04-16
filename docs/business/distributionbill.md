# 铺货单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`distributionbill` 不是普通调拨单，而是“在库货锁定 -> 铺货审核 -> 单件状态切换 -> SKU 库存迁移”的执行宿主。

## 关键入口
- Service: [DistributionBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/distributionbill/DistributionBillServiceImpl.java)

## 真实行为
- `createDistributionBill(...)`
  - 建单时会重新按 EPC 回查单件，重算总件数和总金重，再落单头和明细。
- `updateDistributionBill(...)`
  - 修改时会按 EPC 重建整张明细，并重算合计。
- `confirm(billId)`
  - 审核前会校验单件都在库；审核后会回写单头、明细状态，并推进单件锁定/状态迁移。
- `deleteDistributionBill(id)`
  - 删除时会连带删除整张明细。

## 在主链中的作用
- 铺货执行链
  - 主单负责整条铺货状态与单件迁移。
- `distributionbilldetail`
  - 明细层承接逐件事实和来源补数。

## 风险与待确认
1. `⚠️` `deleteDistributionBill(...)` 是物理删除，未见更强状态保护。
2. `⚠️` `updateDistributionBill(...)` 采用删旧明细后重建模式，中途失败风险较大。
3. `⚠️` `fillData(...)` 为空，单头补数更多依赖下游接口自行拼装。
