# 成品退厂单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
成品退厂单对应 `BillTypeEnum.GOODS_REJECT(06)`，实际覆盖两种业务：
1. `0601 = G_REJECT`：普通成品退厂
2. `0602 = G_SUPPLIER_PACKING`：供应商领料退厂

它不是单纯“出库一张成品单”，而是至少同时驱动三条链：
1. 成品库存扣减/回滚
2. 供应商钱包扣减/回滚
3. 退厂来源挂接回写

其中 `0602` 还会继续联动半成品调拨回流和供应商领料台账。

## 关键入口
- Controller: [GoodsRejectBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/goodsrejectbill/GoodsRejectBillController.java)
- Service: [GoodsRejectBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsrejectbill/GoodsRejectBillServiceImpl.java)
- U10 导入: [GoodsRejectBillImportFromU10Business.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/GoodsRejectBillImportFromU10Business.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_goods_reject_bill` | 主表 | 成品退厂单头 |
| `wh_goods_reject_bill_detail` | 明细表 | SKU、件数、金重、工费、半成品引用等 |
| `wh_goods_reject_record` | 退厂登记池 | `0601` 引用已审核登记单，并把 `rejectBillId/outOfWare` 回写到这里 |
| `wh_semis_transfer` | 半成品调拨单 | `0602` 引用已接收的半成品调拨单，并把 `rejectBillNo` 回写到这里 |
| `wh_sku_stock` | 成品库存 | 审核出库、反审回滚 |
| `wh_wallet` | 供应商钱包 | 按金重和工费生成材质钱包、现金钱包动作 |
| `wh_wallet_log` | 钱包日志 | 记录退厂审核/反审过账痕迹 |

## 建单逻辑
### 1. 主表基础校验
`checkParams()` 只校验 `shopId/wareId/bigClassId/textureId` 等主表基础字段存在，不在建单阶段做库存扣减。

### 2. 明细重算
`convertDetails()` 会以后端口径重算每条明细：
1. 普通退厂 `0601`
   - `cmtFee = goldWeight * costGramCmtFee + num * costItemCmtFee`
2. 供应商领料退厂 `0602`
   - `cmtFee` 不走本单公式，直接取半成品调拨明细 `money`

单头 `totalNum/totalGoldWeight/totalGrossWeight/totalCmtFee` 都由后端汇总回写，不直接信任前端提交值。

### 3. 来源挂接
`handlerRef()` 会按 `bizType` 走两条完全不同的来源链：
1. `0601 = G_REJECT`
   - `refRejectRecord()`
   - 先清空旧登记单上的 `rejectBillId`
   - 再把新引用的登记单挂到当前退厂单
   - 同时把登记单 `outOfWare` 改成 `OUTING`
2. `0602 = G_SUPPLIER_PACKING`
   - `refSemisTransfer()`
   - 先清空旧半成品调拨单上的 `rejectBillNo`
   - 只允许引用 `RECEIVED` 状态且尚未被别的退厂单占用的半成品调拨单
   - 再把 `rejectBillNo` 写回调拨单

所以退厂单本身既是执行单，也是“登记池/半成品回流池”的占用单。

## 审核链
`confirm()` 的主顺序是：
1. 只允许 `INIT/INFIRM`
2. 单头改成 `CONFIRM`，写入 `approvedMan/approvedTime`
3. 查询退厂明细，不能为空
4. `doOutOfWareRejectRecord(id, CONFIRM)`：把当前退厂单关联的登记单 `outOfWare` 统一改成 `OUTED`
5. 组装 `GoodsSkuStockBO`
6. `skuStockService.checkGoodsStock(...)` 先校库存
7. `skuStockService.updateGoodsStock(..., false, OUT, BizTypeEnum.getEnum(bill.getBizType()))`
8. `doUpdateWallet(..., DEBT)`，同时扣减供应商材质钱包和现金钱包
9. `notificationSemisBookkeeping(bill, true)`，仅 `0602` 生效，同步 `SemisSupplierBreakdownApi`

结论上，成品退厂审核不是纯库存动作，而是“库存出库 + 供应商钱包扣账 + 来源状态推进”的复合链。

## 反审链
`infirm()` 的主顺序是：
1. 只允许 `CONFIRM`
2. 跨日反审要权限 `GOODS_REJECT_BILL_CROSS_DAYS`
3. 单头改成 `INFIRM`，清空审核人和审核时间
4. `doOutOfWareRejectRecord(id, INFIRM)`，把关联登记单 `outOfWare` 改回 `OUTING`
5. `skuStockService.updateGoodsStock(..., true, OUT, BizTypeEnum.getEnum(...))`
6. `doUpdateWallet(..., DEPOSIT)`，回补供应商材质钱包和现金钱包
7. `notificationSemisBookkeeping(bill, false)`，回滚 `0602` 的供应商领料台账变化

## 钱包链
`doUpdateWallet()` 的口径固定是两笔：
1. 材质钱包
   - `balance = 明细 goldWeight 合计`
   - 钱包材质由单头 `textureId` 推导
2. 现金钱包
   - `balance = 明细 cmtFee 合计`
   - 四舍五入到 0 位后落账

方向规则：
1. 审核：`DEBT`
2. 反审：`DEPOSIT`

## 0602 供应商领料退厂附加链
### 1. 半成品调拨来源占用
`refSemisTransfer()` 会把被引用的半成品调拨单头 `rejectBillNo` 挂到当前退厂单号上。

### 2. 供应商领料台账
`notificationSemisBookkeeping()` 只对 `0602` 生效：
1. 通过 `rejectBillNo` 反查半成品调拨单
2. 再取调拨明细 `num/gramWeight/money`
3. 同步到 `SemisSupplierBreakdownApi.syncBreakdownChange(...)`

### 3. 反向生成转成品入库
`getPickingDetails()` / `buildInboundCreate()` 会根据当前 `0602` 退厂单号，反查半成品调拨链，自动生成转成品入库草稿。

## 更新、删除、作废
### 更新
`update()` 只允许 `INIT/INFIRM`，会重算单头和明细，并重新走 `handlerRef()` 重绑来源。

### 作废
`discard()` 只允许 `INIT/INFIRM`，会单头改成 `DISCARD`，再 `handlerRef(bill, emptyList())` 解除登记池或半成品调拨池挂接。

### 删除
`delete(Long id)` 当前直接 `return false`，没有真实删除实现。

## 当前结论
1. 成品退厂单是“成品库存扣减 + 供应商钱包扣减 + 来源挂接推进”的复合链。
2. `0601` 承接退厂登记池，`0602` 承接已接收半成品调拨单。
3. `0602` 审核/反审还会继续同步供应商领料台账，并能反向生成转成品入库草稿。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `delete(Long id)` | 成品退厂单 | 删除接口当前直接返回 `false`，没有真实实现 | 待确认 | 确认前端是否已禁用删除，或后端是否遗漏实现 |
| ⚠️ | `doOutOfWareRejectRecord` | 成品退厂单 / 退厂登记单 | 反审时把登记池状态回写成 `OUTING`，不是 `NOT_OUT` | 待确认 | 确认这是否表示“仍被退厂单占用但已撤销出库” |
| ⚠️ | `doUpdateWallet` | 成品退厂单 | 现金钱包金额直接取明细工费合计并四舍五入到整数，精度口径偏强 | 待确认 | 确认退厂工费是否业务上只允许整元 |
