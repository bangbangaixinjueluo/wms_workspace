# 辅料入库单

## 1. 模块定位

- 对应表：`wh_accessory_entry_bill`、`wh_accessory_entry_bill_detail`
- 入口：`AccessoryEntryBillController`、`AccessoryBusiness`、`AccessoryEntryBillServiceImpl`
- 角色：辅料入库执行宿主，负责单头、明细、审核流、导出和半成品调拨引用口径

## 2. 主链结论

- 状态流：`INIT -> CONFIRM -> INFIRM / DISCARD`
- 支持 `saveAndConfirm`
- 反审前会校验是否被半成品调拨单通过 `semisTransferNo` 引用
- 具备跨日反审权限 `ACCESSORY_ENTRY_BILL_CROSS_DAYS`

## 3. 核心职责

- 建单时生成 `BillTypeEnum.ACCESSORY` 单号
- 校验单头必填：网点、仓库、大类、材质、供应商、是否付款、业务日期
- 校验明细金额：`unitPrice * qty = totalAmount`
- 汇总 `amountSum`
- 管理单头审核信息 `approvedMan/approvedTime`
- 管理明细与半成品调拨单的挂接字段 `semisTransferNo`

## 4. 代码结论

- `AccessoryBusiness.create/update` 是真实宿主，service 负责保存与状态切换
- `confirm(...)` 只做状态校验和明细金额校验，然后调用 `accessoryEntryBillService.confirm(...)`
- 当前代码里没看到类似 `goodsStock/materialStock/accessoryStock` 的真实库存增量回写
- `referencesDetails(...)` 会把被引用的入库明细挂上半成品调拨单号，并在重挂时先清旧引用

## 5. 关注点

- `⚠️` 当前审核/反审只看到单据状态流，没看到真实辅料库存和库存日志写入，是否由别处承接需要继续确认
- `⚠️` 反审阻断强依赖 `semisTransferNo`，说明这张单已经是半成品调拨来源池
- `⚠️ getDetailPage(...)` 最终 `total` 直接用 `records.size()`，不是分页总数
- `⚠️` 明细关联查询里 `leftJoin(AccessoryEntryBillDetailDO::getBillId, AccessoryEntryBillDetailDO::getId)` 看起来像是 join 键写反，需要确认
