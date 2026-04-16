# 单件转料单模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`recyclebill` 是“成品转原料”的真实执行宿主，不只是单头。它会同时改原料库存、成品库存、单件状态和单件日志。

## 关键入口
- Service: [RecycleBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/recyclebill/RecycleBillServiceImpl.java)

## 真实行为
- `submit(id)`
  - 审核时会增加原料库存、扣减成品库存，并把单件状态推进到转料完成链。
- `unConfirm(id)`
  - 反审时整条镜像回滚，并校验跨日权限 `RECYCLE_BILL_CROSS_DAYS`。
- `updateRecycleBill(...)`
  - 修改时会删旧明细再按 billNo 全量重建。
- `discard(id)`
  - 作废会直接把单头状态改成作废。

## 在主链中的作用
- `recycledetail`
  - 明细层承接逐件事实；主单承接库存和单件状态回写。

## 风险与待确认
1. `⚠️` `updateRecycleBill(...)` 采用删旧明细后重建模式。
2. `⚠️` `deleteRecycleBill(...)` / `deleteRecycleDetailByBillNo(...)` 都是物理删除。
3. `⚠️` 审核/反审同时改多条库存和单件链，任何一侧口径变更都容易账实不平。
