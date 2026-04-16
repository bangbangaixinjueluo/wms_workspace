# 盘点称重单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
盘点称重单对应 `BillType=37`，业务类型至少包含：
1. `3701 = 成品称重`
2. `3702 = 原料称重`

它不是直接改库存的执行单，而是“称重盘点录入 -> 审核时计算账实差 -> 反审清空差值”的盘点分析单：
1. 建单时录入本次实盘结果
2. 系统会自动补齐当前仓库中未录入的款式/原料明细
3. 审核时按账面库存计算每行盈亏件数/金重
4. 单头只保存差值结果，不直接修改 `wh_sku_stock` 或 `wh_material_stock`
5. 反审时清空差值，恢复到未审核分析状态

## 关键入口
- Controller: [InventoryWeighingBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/inventoryweighingbill/InventoryWeighingBillController.java)
- Service: [InventoryWeighingBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/inventoryweighingbill/InventoryWeighingBillServiceImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_inventory_weighing_bill` | 主表 | 盘点称重单头 |
| `wh_inventory_weighing_detail` | 明细表 | 录入的称重结果及审核后盈亏差值 |
| `wh_inventory_weighing_bill_stock` | 库存备份 | 成品称重审核时备份账面库存 |
| `wh_material_stock` | 原料账面库存 | 原料称重审核时作为差值对比基线 |
| `wh_sku_stock` | 成品账面库存 | 成品称重审核时作为差值对比基线 |

## 建单与改单
### 1. 建单
`createInventoryWeighingBill(...)` 的核心步骤是：
1. 明细不能为空
2. `3701` 必须选 `skuId`
3. `3702` 必须选 `materialId`
4. 生成 `billNo`
5. 校验明细：
   - 金重大于 0
   - 同一张单里原料/款式不能重复
6. 插入单头
7. 汇总：
   - `totalNum`
   - `totalGoldWeight`
   - `totalGrossWeight`
   - `totalTareWeight`
8. 生成并保存明细

### 2. 系统自动补明细
这个模块最关键的特点是：建单时会把“没录到但账面存在”的对象也自动补进明细。

原料称重 `3702`：
1. 先保留用户已录原料
2. 再查询当前仓库原料库存
3. 对未出现在录入列表中的原料自动追加明细
4. 自动追加明细的数量和重量都先置 0，并标记 `isGen = 1`

成品称重 `3701`：
1. 先保留用户已录款式
2. 再查询当前仓库指定大类/材质下的成品库存
3. 对未录入的 SKU 自动追加明细
4. 同样标记 `isGen = 1`

所以这张单本质上是“本次实盘录入 + 账面补全集”的混合明细池。

### 3. 改单
`updateInventoryWeighingBill(...)` 只允许：
1. `INIT`
2. `INFIRM`

更新时会：
1. 重做全部明细校验
2. 重算单头合计
3. 更新主表
4. 先删光旧明细
5. 再按“用户录入 + 系统自动补齐”的规则整张重建

## 审核链
`confirm(Long id)` 的核心动作不是改库存，而是“计算差值”：
1. 校验状态可审核
2. 明细不能为空
3. 单头改 `CONFIRM`
4. 调 `doComputeProfitLoss(...)`

原料称重 `3702`：
1. 逐行取 `wh_material_stock`
2. `profitLossGoldWeight = 实盘金重 - 账面余额`
3. 明细写回 `profitLossGoldWeight`
4. 单头聚合 `totalProfitLossGoldWeight`

成品称重 `3701`：
1. 逐行取 `wh_sku_stock`
2. `profitLossNum = 实盘件数 - 账面件数`
3. `profitLossGoldWeight = 实盘金重 - 账面金重`
4. 明细写回差值
5. 单头聚合：
   - `totalProfitLossNum`
   - `totalProfitLossGoldWeight`
6. 同时调用 `inventoryWeighingBillStockService.initInventoryWeighingBillStock(...)` 记录账面库存备份

这里要注意：代码没有看到审核时直接改库存，只是把差异算出来并保存。

## 反审与作废
### 1. 反审
`unConfirm(Long id)` 的核心动作是：
1. 只允许已审核单据反审
2. 若跨日，需要权限 `PERMIT_INVENTORY_BILL_CROSS_DAYS`
3. 单头改 `INFIRM`
4. 清空单头：
   - `totalProfitLossGoldWeight = 0`
   - `totalProfitLossNum = 0`
5. 清空所有明细差值字段

### 2. 作废
`obsolete(Long id)` 只改单头状态到 `DISCARD`，不做库存或差值回滚，因为它依赖状态机保证只能对未执行完成的单据作废。

### 3. 删除
`deleteInventoryWeighingBill(Long id)` 会：
1. 删主表
2. 删全部明细

当前没看到状态限制。

## 查询口径
1. 列表页会按登录人仓库权限过滤
2. `pageInventory(...)` 中它和单件盘点单共用盘点总列表入口
3. 详情展示时会把 `isGen = 1` 的系统生成明细过滤掉
4. 但盈亏汇总计算又会先基于全明细求值

所以“用户看到的详情明细”和“系统真正用来算差值的明细全集”不是同一组数据。

## 当前结论
1. 盘点称重单不是库存执行单，而是盘点差异分析单。
2. 审核真正做的是“账实差值落账到盘点单本身”，不是直接修改原料或成品库存。
3. 系统生成明细 `isGen = 1` 是这个模块的关键设计，用来保证“未录入但账面存在”的对象也参与差值计算。
4. 它和单件盘点单在列表层是同一个盘点域入口，但业务含义完全不同：一个冻结单件快照，一个计算称重差值。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `confirm` | 盘点称重单 | 审核当前只计算并保存盈亏差值，没看到正式回写 `wh_sku_stock` 或 `wh_material_stock` | 待确认 | 确认差异生效是否落在别的盘点处理流程 |
| ⚠️ | `deleteInventoryWeighingBill` | 盘点称重单 | 删除主表和明细时没看到状态限制 | 待确认 | 确认是否只允许删除草稿/反审态 |
| ⚠️ | `fillData/handleDetailList` | 盘点称重单 | 详情页会过滤 `isGen = 1` 明细，但汇总又依赖全量明细，前后端容易对不上口径 | 待确认 | 确认页面汇总是否明确按“全量差值”展示 |
| ⚠️ | `doProfitLossAccessory` | 盘点称重单 | 只有成品称重审核时才初始化 `inventoryWeighingBillStock` 账面备份，原料称重未见对应备份表动作 | 待确认 | 确认原料侧是否不需要单据级库存快照 |
