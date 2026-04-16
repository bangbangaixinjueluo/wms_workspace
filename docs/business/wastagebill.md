# 损耗单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责损耗单的创建、修改、审核、反审、作废与导出。它和盈亏调整单同属库存链，但业务口径更明确：损耗单只允许登记正向损耗，审核时扣减库存，反审时再加回。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|------|------|
| `wh_wastage_bill` | 读写 | 损耗单主表，对应 `WastageBillDO` |
| `wh_wastage_detail` | 读写 | 损耗明细；支持饰品损耗和熔金损耗两种口径 |
| `wh_material_stock` / `wh_material_stock_log` | 读写 | 熔金损耗审核/反审时更新原料库存和库存日志 |
| 成品库存相关表 | 读写 | 饰品损耗审核/反审时通过 `SkuStockService.updateGoodsStock(...)` 扣减或回滚成品库存 |

## 模块依赖
- `CommonService`：生成单号、获取当前登录人
- `SkuStockService`：饰品损耗时检查并更新成品库存
- `MaterialStockService`：熔金损耗时更新原料库存
- `PermissionApi`：跨日反审时校验 `WASTAGE_BILL_CROSS_DAYS`
- `SkuService` / `MaterialService` / `ShopService` / `WarehouseService` / `TextureService` / `BigClassService`：回填展示数据

## 接口列表

### createWastageBill
> `WastageBillController.java#createWastageBill()` -> `WastageBillServiceImpl.java#createWastageBill()` `L41 / L96`

- 入口类型：HTTP 接口
- 用途：创建损耗单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 业务逻辑：
  1. 强制明细非空
  2. 生成损耗单号
  3. `validDetails()` 校验明细合法性
  4. `setTotalHeadData()` 重算单头合计
     - `3802` 熔金损耗：汇总熔前、熔后、损耗三组重量
     - 其他：仅汇总 `totalWastageWeight`
  5. 写入主表和明细

### updateWastageBill
> `WastageBillController.java#updateWastageBill()` -> `WastageBillServiceImpl.java#updateWastageBill()` `L49 / L169`

- 入口类型：HTTP 接口
- 用途：修改损耗单
- 前置条件：
  - 单据必须存在
  - 仅允许 `INIT(0)`、`INFIRM(3)` 修改
- 业务逻辑：
  1. 重新校验明细
  2. 重算单头损耗汇总值
  3. 更新主表
  4. 删除旧明细并重建

### confirm
> `WastageBillController.java#confirm()` -> `WastageBillServiceImpl.java#confirm()` `L102 / L356`

- 入口类型：HTTP 接口
- 用途：审核损耗单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 状态变更：
  - `wh_wastage_bill.bill_status -> CONFIRM(2)`
  - 写入 `approvedMan/approvedTime`
- 业务逻辑：
  1. `BillStatusEnum.confirmCheck()` 校验当前状态
  2. 校验明细非空
  3. 熔金损耗：`doMaterialStock(..., NEGATIVE)` 扣减原料库存并记日志
  4. 饰品损耗：`doGoodsStock(..., CONFIRM)` 扣减成品库存
- 关键结论：
  - 审核时库存方向固定是“扣减”
  - 饰品损耗和熔金损耗可以共用单据骨架，但不会同时走两条库存链

### unConfirm
> `WastageBillController.java#unConfirm()` -> `WastageBillServiceImpl.java#unConfirm()` `L110 / L483`

- 入口类型：HTTP 接口
- 用途：反审损耗单
- 事务边界：`@Transactional(rollbackFor = Exception.class)`
- 前置条件：
  - 当前状态必须为 `CONFIRM(2)`
  - 若单据日期跨日，当前登录人必须拥有 `WASTAGE_BILL_CROSS_DAYS`
- 状态变更：
  - `wh_wastage_bill.bill_status -> INFIRM(3)`
  - 清空 `approvedMan/approvedTime`
- 业务逻辑：
  1. 先把单头改为已反审
  2. 熔金损耗：`doMaterialStock(..., POSITIVE)` 把原料库存加回
  3. 饰品损耗：`doGoodsStock(..., INFIRM)` 通过 `rollback = true` 回滚成品库存

### obsolete
> `WastageBillController.java#discard()` -> `WastageBillServiceImpl.java#obsolete()` `L118 / L343`

- 入口类型：HTTP 接口
- 用途：作废损耗单
- 业务逻辑：
  1. 校验单据存在
  2. 调用 `BillStatusEnum.obsoleteCheck()`
  3. 把状态改为 `DISCARD(4)`

## 业务规则

### 损耗单只接受正向损耗
1. `validDetails()` 强制 `wastageWeight > 0`
2. 熔金损耗还要求：
   - `smeltingBeforeWeight > 0`
   - `smeltingAfterWeight > 0`
   - `smeltingAfterWeight <= smeltingBeforeWeight`
   - `wastageWeight < smeltingBeforeWeight`
3. 也就是说，创建和更新阶段就不接受负损耗

### `bizType` 决定两条完全不同的库存链
1. `3801` 饰品损耗
   - 使用单头 `bigClassId/textureId`
   - 审核前先 `checkGoodsStock(...)`
   - 审核时调用 `updateGoodsStock(..., StockInOutEnum.OUT, ...)`
2. `3802` 熔金损耗
   - 按明细 `materialNo` 聚合
   - 审核时生成 `MaterialStockLogDO`
   - 通过 `updateMaterialStock(...)` 更新原料库存

### 熔金损耗单头合计不是直接抄明细损耗
1. `totalSmeltingBeforeWeight` = 明细熔前合计
2. `totalSmeltingAfterWeight` = 明细熔后合计
3. `totalWastageWeight` = 熔前合计 - 熔后合计
4. 所以熔金损耗的单头“损耗合计”来自前后重量差，不是简单求和 `wastageWeight`

### 饰品损耗只扣金重，不扣件数
1. `GoodsSkuStockBO.buildInOne(...)` 里传入的 `stock = 0`
2. 实际扣减的是损耗金重
3. 成品侧仍会做库存充足校验：`checkGoodsStock(..., 0, totalWastageWeight)`

## 当前结论
- 损耗单和盈亏调整单都属于库存链，但语义不同：盈亏调整允许正负差异，损耗单只登记正损耗。
- `3801` 饰品损耗只走成品库存；`3802` 熔金损耗只走原料库存和库存日志。
- 损耗单比盈亏调整单多了一层跨日反审权限控制。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteWastageBill` | 损耗单 / 损耗明细 | 直接删除主表和明细，未见状态限制；若误删已审核单据，代码本身没有保护 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | `doGoodsStock` | 损耗单 / 成品库存 | 饰品损耗传入 `stock = 0`，只按金重扣库存；需确认业务上是否明确“不扣件数只扣重量” | 待确认 | 确认饰品损耗口径是否只看金重 |
| ⚠️ | `setTotalHeadData` | 损耗单 | 熔金损耗单头 `totalWastageWeight` 取“熔前合计 - 熔后合计”，不是明细 `wastageWeight` 求和；若两者口径不一致会出现单头/明细不平 | 待确认 | 确认前端和报表是否按同一公式展示 |
