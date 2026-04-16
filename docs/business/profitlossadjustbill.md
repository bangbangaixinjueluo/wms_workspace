# 盈亏调整单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责盈亏调整单的创建、修改、审核、反审、作废与导出。它本质上不是资金类单据，而是库存类调整单：审核时把明细中的盈亏件数/盈亏金重写入成品库存或原料库存；反审时按相反方向回滚。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|------|------|
| `wh_profit_loss_adjust_bill` | 读写 | 盈亏调整单主表，对应 `ProfitLossAdjustBillDO` |
| `wh_profit_loss_adjust_detail` | 读写 | 调整明细，按 `bizType` 区分饰品调整/原料调整 |
| `wh_material_stock` / `wh_material_stock_log` | 读写 | 原料调整审核/反审时更新库存余额与库存日志 |
| 成品库存相关表 | 读写 | 饰品调整审核/反审时通过 `SkuStockService.updateGoodsStockAdjust(...)` 联动更新 |

## 模块依赖
- `CommonService`：生成单号、获取当前登录人
- `ProfitLossAdjustDetailService`：批量保存明细、聚合原料库存差额
- `MaterialStockService`：执行原料库存和库存日志更新
- `SkuStockService`：执行成品库存调整
- `ShopService` / `WarehouseService`：回填展示字段

## 接口列表

### createProfitLossAdjustBill
> `ProfitLossAdjustBillController.java#createProfitLossAdjustBill()` -> `ProfitLossAdjustBillServiceImpl.java#createProfitLossAdjustBill()` `L41 / L83`

- 入口类型：HTTP 接口
- 用途：创建盈亏调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 业务逻辑：
  1. 按单据日期生成 `billNo`
  2. 汇总明细 `num/goldWeight` 到单头 `totalNum/totalGoldWeight`
  3. 写入主表
  4. `doSaveDetails()` 按 `bizType` 校验明细主键口径
     - `3601`：必须有 `skuNo`
     - `3602`：必须有 `materialNo`
  5. 批量写入明细表

### updateProfitLossAdjustBill
> `ProfitLossAdjustBillController.java#updateProfitLossAdjustBill()` -> `ProfitLossAdjustBillServiceImpl.java#updateProfitLossAdjustBill()` `L49 / L152`

- 入口类型：HTTP 接口
- 用途：修改盈亏调整单
- 前置条件：
  - 单据必须存在
  - 仅允许 `INIT(0)`、`INFIRM(3)` 修改
- 业务逻辑：
  1. 重算单头汇总值
  2. 更新主表
  3. 删除旧明细
  4. 重新插入新明细

### confirm
> `ProfitLossAdjustBillController.java#confirm()` -> `ProfitLossAdjustBillServiceImpl.java#confirm()` `L103 / L275`

- 入口类型：HTTP 接口
- 用途：审核盈亏调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 状态变更：
  - `wh_profit_loss_adjust_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedMan/approvedTime`
- 业务逻辑：
  1. `BillStatusEnum.confirmCheck()` 校验当前状态可审核
  2. 校验明细非空
  3. 先落单头审核状态
  4. `adjustStock()` 按 `bizType` 执行库存写回
     - `3601 ACCESSORY_ADJUST`：调用 `SkuStockService.updateGoodsStockAdjust(...)`
     - `3602 MATERIAL_ADJUST`：调用 `MaterialStockService.updateMaterialStock(...)`
- 关键结论：
  - 盈亏调整单不碰钱包、不碰银行卡
  - 审核真正落点是“库存余额 + 库存日志”

### unConfirm
> `ProfitLossAdjustBillController.java#unConfirm()` -> `ProfitLossAdjustBillServiceImpl.java#unConfirm()` `L111 / L353`

- 入口类型：HTTP 接口
- 用途：反审盈亏调整单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：当前状态必须为 `CONFIRM(2)`
- 状态变更：
  - `wh_profit_loss_adjust_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedMan/approvedTime`
- 业务逻辑：
  1. 先把单头改成已反审
  2. 再次调用 `adjustStock()`
  3. 通过 `billStatus = INFIRM` 让库存服务走回滚方向

### obsolete
> `ProfitLossAdjustBillController.java#obsolete()` -> `ProfitLossAdjustBillServiceImpl.java#obsolete()` `L119 / L267`

- 入口类型：HTTP 接口
- 用途：作废盈亏调整单
- 业务逻辑：
  1. 校验单据存在
  2. 调用 `BillStatusEnum.obsoleteCheck()`
  3. 把状态改为 `DISCARD(4)`

## 业务规则

### 盈亏调整单是库存调整单，不是资金调整单
1. 审核/反审只会进入库存链
2. 不会写 `wh_wallet`、`wh_shop_bank`
3. 原料和成品分别走不同库存服务

### `bizType` 决定库存口径
1. `3601`：饰品调整
   - 明细必须带 `skuNo`
   - 审核时走 `updateGoodsStockAdjust(...)`
2. `3602`：原料调整
   - 明细必须带 `materialNo`
   - 审核时走 `computeForStock(...) + updateMaterialStock(...)`

### 盘盈/盘亏通过明细正负值表达
1. 代码里没有独立“盘盈/盘亏状态位”
2. 成品侧直接把 `detail.num/goldWeight` 原样送入库存调整
3. 原料侧直接把 `goldWeight` 聚合成库存差额
4. 反审不是重新算一套负数，而是通过 `billStatus = INFIRM` 切到回滚方向

### 原料调整会同时记库存日志
1. `convertToStockLog()` 组装 `bizType/bizId/bizNo/bizDate/billStatus/shopId/wareId/billType`
2. `StockAccountingTypeEnum`
   - 审核：`POSITIVE`
   - 反审：`NEGATIVE`
3. 最终由 `MaterialStockService.updateMaterialStock(stockDto.setAdjust(true))` 统一落库存和日志

## 当前结论
- 盈亏调整单的核心不是“财务盈亏”，而是“盘点差异落库存”。
- 它和前面的收款单、付款单、调账单完全不同，真正被改写的是库存账，不是资金账。
- `inventoryId/inventoryBillNo/monthlyInventory` 目前更像来源追踪字段，当前服务实现里没看到反向驱动盘点称重单的回写逻辑。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteProfitLossAdjustBill` | 盈亏调整单 / 调整明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | `createProfitLossAdjustBill/updateProfitLossAdjustBill` | 盈亏调整单明细 | `validDetails()` 已写好但被注释，当前未强制拦截“件数为空/为 0、金重为空/为 0”等异常明细 | 待确认 | 确认这是否是有意放宽，还是遗留漏接 |
| ⚠️ | `updateProfitLossAdjustBill` | 盈亏调整单 | 重算 `totalGoldWeight` 时未过滤 `null`，如果更新请求中某条明细 `goldWeight` 为空，存在空指针风险 | 待确认 | 确认前端是否永远保证非空，或后端补 `filter(Objects::nonNull)` |
| ⚠️ | `inventoryId/inventoryBillNo/monthlyInventory` | 盈亏调整单 / 盘点称重单 | 当前只看到字段存储和查询条件，未看到对盘点称重单状态或累计值的自动回写 | 待确认 | 确认是否只是追溯字段，还是少了一段回写链 |
