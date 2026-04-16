# 辅料出库单

## 1. 模块定位

- 对应表：`wh_accessory_out_bill`、`wh_accessory_out_bill_detail`
- 入口：`AccessoryOutBillController`、`AccessoryBusiness`、`AccessoryOutBillServiceImpl`
- 角色：辅料出库执行宿主，承接建单、审核、反审、作废、导出和明细分页

## 2. 主链结论

- 状态流：`INIT -> CONFIRM -> INFIRM / DISCARD`
- 支持 `saveAndConfirm`
- 具备跨日反审权限 `ACCESSORY_OUT_BILL_CROSS_DAYS`

## 3. 核心职责

- 建单时生成 `BillTypeEnum.ACCESSORY_OUT` 单号
- 校验单头必填：网点、仓库、大类、材质、业务日期
- 校验明细金额：`unitPrice * qty = totalAmount`
- 汇总 `amountSum`
- 管理单头审核信息

## 4. 代码结论

- 真实宿主同样在 `AccessoryBusiness`
- `confirmOutBill(...)` 只做状态与明细校验，再切单头状态
- `infirmOutBill(...)` 只做跨日权限和状态校验，当前没看到真实库存回滚
- 出库明细分页和导出与入库链做法基本镜像

## 5. 关注点

- `⚠️` 当前审核/反审同样没看到真实 `AccessoryStock`/`AccessoryStockLog` 回写，说明“出库单”和“库存账”可能尚未接线
- `⚠️ buildConditions(...)` 里 `shopIds` 过滤误写成了 `status` 字段
- `⚠️ buildDetailConditions(...)` 的 `payed` 过滤误用了 `AccessoryEntryBillDO::getPayed`
- `⚠️ getOutBillOptions()` 当前直接返回 `null`
