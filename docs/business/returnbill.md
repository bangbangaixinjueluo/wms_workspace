# 客户退货单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
客户退货单对应 `BillTypeEnum` 下的 `wh_return_bill` 主表，业务类型固定是 `1901-销售退货`。它不是单纯“商品退回仓库”，而是把三条链一起落账：
1. 单件/成品库存回库
2. 客户钱包回写
3. 结价分支冲回

其中单件退货和成品退货共用同一张单，只是明细是否带 `epc` 不同。

## 关键入口
- Controller: [ReturnBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/returnbill/ReturnBillController.java)
- 审核业务: [ReturnBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/ReturnBillBusiness.java)
- 主服务: [ReturnBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/returnbill/ReturnBillServiceImpl.java)
- 结算明细服务: [ReturnSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/returnsettle/ReturnSettleServiceImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_return_bill` | 主表 | 退货单头，记录客户、仓库、欠料欠款快照、结算合计 |
| `wh_return_detail` | 明细表 | 单件或成品退货明细 |
| `wh_return_settle` | 结算表 | 转欠/结价/税费等结算方式明细 |
| `wh_item` | 单件表 | 单件退货审核/反审时改状态和仓库 |
| `wh_item_log` | 单件日志 | 记录单件退货入库/回退痕迹 |
| `wh_sku_stock` | 成品库存 | 成品退货和单件退货审核/反审都会动 |
| `wh_wallet` | 客户钱包 | 记录材质钱包和现金钱包 |
| `wh_wallet_log` | 钱包日志 | 记录退货钱包过账 |

## 建单逻辑
### 1. 结算先落
`doCreateReturnBill()` 先校验并落 `wh_return_settle`：
1. 结算明细不能为空
2. 若单头 `totalSaleCmtFee > 0`，结算清单里必须带工费合计项
3. `RECIRCULATION(01)` 时必须有 `materialNo / settleClass / settleWeight`
4. `CLOSING_PRICE(02)` 时必须有 `materialNo / settleClass`
5. 单头 `totalSettleMoney` 取结算金额合计后再取负
6. 单头 `totalSettleWeight` 取转欠重量合计后再取负

因此退货单头上的“本单应收款/本单欠料”是按结算明细汇总出来的，不是按商品明细直接算。

### 2. 明细拆成两类
审核业务里把明细分成：
1. `epc` 非空：单件退货
2. `epc` 为空：成品退货

两类明细共用一张单，但回库动作不同。

### 3. 客户和原单校验
建单时会校验：
1. 单件当前必须还是“已销售”状态
2. 若单件带销售单号，则销售单上的客户必须和当前退货客户一致
3. 维护原始网点/仓库/库位/单件状态，给后续反审恢复使用

## 审核链
`ReturnBillBusiness.confirm()` 的主顺序是：
1. 校验单头存在、退入库位不为空
2. `BillStatusEnum.confirmCheck(...)`
3. 单头状态改成 `CONFIRM`
4. 按 `epc` 把明细拆成单件退货和成品退货
5. 单件退货：
   - 单件状态改成 `ON_WARE`
   - 退回当前网点/仓库/库位
   - 解锁单件
   - 写 `wh_item_log`
   - 生成单件库存增量
6. 成品退货：
   - 生成 `GoodsSkuStockBO`
   - `updateGoodsStock(..., IN, BizTypeEnum.getEnum(returnBill.getBizType()))`
7. 维护关联维修单流转仓库
8. 校验结算项里 `settleType == 1` 的 `goldPrice != 0`
9. 若税额不为 0，则结算清单必须带税费项
10. 钱包主账 `handlerNotCloseWallet(..., DEPOSIT)`
11. 结价冲回 `handlerCloseWallet(..., DEBT)`
12. `increaseStock(...)` 再补一层单件库存累计

## 反审链
`ReturnBillBusiness.infirm()` 的主顺序是：
1. 跨日反审需要权限 `RETURN_BILL_CROSS_DAYS`
2. `BillStatusEnum.infirmCheck(...)`
3. 单头状态改成 `INFIRM`
4. 校验所有退回单件当前仍在本退货单仓库，且未被锁定
5. 单件明细恢复原始状态、原始仓库/库位
6. 回滚关联维修单流转仓库
7. 成品库存 `updateGoodsStock(..., true, IN, ...)`
8. 钱包主账 `handlerNotCloseWallet(..., DEBT)`
9. 结价冲回 `handlerCloseWallet(..., DEPOSIT)`
10. `decreaseStock(...)` 回滚单件库存累计

## 钱包链
### 主账
`handlerNotCloseWallet()` 的口径是：
1. 先把所有带 `materialNo` 的结算行按 `materialNo + settleClass` 聚合 `settleWeight`
2. 再额外追加一条现金钱包：
   - `materialNo = CASH`
   - `balance = totalSettleMoney` 取整后再 `negate()`

审核时传 `DEPOSIT`，反审时传 `DEBT`。

因此客户退货的主账语义是：
- 材质钱包增加
- 现金钱包按“退货应收款的相反数”一起并入主账

### 结价冲回
`handlerCloseWallet()` 只处理 `settleName == CLOSING_PRICE.getText()` 的结算行：
1. 审核时传 `DEBT`
2. 反审时传 `DEPOSIT`

这延续了结价链的统一逻辑：
- 主账先整体入账
- 再把结价部分按相反方向冲回

## 维修单联动
如果退货明细关联了维修单明细：
1. 审核时把维修单明细 `stepWareId` 改成当前退入仓库
2. 反审时恢复 `lastStepWareId`

因此客户退货单不只是库存/钱包链，也会改维修流转仓库。

## 更新、删除、作废
### 更新
主服务会先删旧明细和旧结算，再重走建单逻辑。

### 删除
`deleteReturnBill()` 会级联删除：
1. `wh_return_detail`
2. `wh_return_settle`
3. 主表

### 作废
`obsolete()` 只改状态到 `DISCARD`，没有库存/钱包回滚逻辑，因此只能理解为未审核单据作废。

## 当前结论
1. 客户退货单是“库存回库 + 客户钱包回写 + 结价冲回 + 维修仓位联动”复合链。
2. 单头 `totalSettleMoney/totalSettleWeight` 来自结算明细汇总，不是商品明细直接汇总。
3. 结价在退货链里同样不是独立单据，而是宿主退货单审核/反审中的一笔反向钱包冲回。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `handlerCloseWallet` | 客户退货单 / 结价 | 结价识别依赖 `settleName == CLOSING_PRICE.getText()`，不是稳定 `settleNo` | 待确认 | 确认是否应统一改成按 `settleNo=02` 识别 |
| ⚠️ | `deleteReturnBill` | 客户退货单 | 删除逻辑未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | `obsolete` | 客户退货单 | 作废只改单头状态，不回滚库存和钱包，必须依赖状态机保证未审核单才可作废 | 待确认 | 核对 `BillStatusEnum.obsoleteCheck()` 精确可作废状态 |
