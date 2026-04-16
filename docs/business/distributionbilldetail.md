# 铺货单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`distributionbilldetail` 不是普通子表，而是铺货链的事实层。SKU、品类、图片、产品包、销售单来源、源/目标门店仓库以及状态展示，都是在这层补齐。

## 关键入口
- Service: [DistributionBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/distributionbilldetail/DistributionBillDetailServiceImpl.java)

## 真实行为
- `getDistributionBillDetailVOPage(pageReqVO)`
  - 分页时会补 SKU、品类、包号、销售单号、源/目标仓店和铺货状态。
- `getDistributionBillDetailVOList(exportReqVO)`
  - 导出路径同样会补单头源/目标仓店信息。
- `createDistributionBillDetailList(detailList)`
  - 铺货单明细支持整批创建。
- `deleteByBillId(billId)`
  - 按单头整批物理删除铺货明细。

## 在主链中的作用
- `distributionbill`
  - 铺货单主头承接流转，明细层承接实际铺货事实和来源挂接。

## 风险与待确认
1. `⚠️` `deleteByBillId(...)` 是物理删除，未见主单状态保护。
2. `⚠️` 导出/分页都依赖再查单头、仓库、门店补数，字段口径若有一处改动，明细展示会整体漂移。
3. `⚠️` `getDistributionBillDetailVOList(exportReqVO)` 里默认直接使用 `distributionBillDO`，若单头缺失会有空指针风险。
