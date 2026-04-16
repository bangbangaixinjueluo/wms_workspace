# 单件盘点单操作
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
单件盘点单操作对应 `BillTypeEnum.ITEM_INVENTORY_BILL`，主业务类型固定为：
1. `5201 = 单件盘点`

它不是“审核即改库存”的执行单，而是一个“盘点基线快照 + 批次采集 + 差异查询”模块：
1. 建单时先把当前仓库可盘单件整体快照到 `wh_item_inventory_bill_item`
2. 同时把仓库维度库存汇总快照到 `wh_item_inventory_bill_stock`
3. 后续按批次回填“本次盘到”的 EPC，并额外登记盘盈 EPC
4. 审核/反审当前只改单头状态，不直接回写 `wh_item` 或 `wh_sku_stock`
5. 真正的业务价值落在“待盘清单、批次结果、差异报表、统计报表”这几类查询口径

## 关键入口
- Controller: [ItemInventoryBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/iteminventorybill/ItemInventoryBillController.java)
- 操作 Controller: [ItemInventoryOptController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/iteminventorybill/ItemInventoryOptController.java)
- Service: [ItemInventoryBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/iteminventorybill/ItemInventoryBillServiceImpl.java)
- Mapper XML: [ItemInventoryBillMapper.xml](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/resources/mapper/iteminventorybill/ItemInventoryBillMapper.xml)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_item_inventory_bill` | 主表 | 单件盘点单头 |
| `wh_item_inventory_bill_batch` | 批次表 | 一张盘点单可拆多个采集批次 |
| `wh_item_inventory_bill_item` | 盘点明细池 | 建单时先灌入“仓内可盘单件快照”，后续再标记盘到/盘盈 |
| `wh_item_inventory_bill_stock` | 库存备份表 | 建单时备份仓库当前单件库存汇总 |
| `wh_item` | 单件表 | 建单时作为快照来源；查询盘盈/待盘时也会直接读 |
| `wh_warehouse_location` | 库位表 | 按库位盘点时校验批次归属并回填区域/库位 |
| `wh_texture` | 材质表 | 建单时按仓库材质配置拼接 `textureNames` |

## 建单与初始化
### 1. 建单前校验
`createInventoryBill(ItemInventoryOptBillStoreVO)` 会先做三层前置检查：
1. 仓库必须存在
2. 仓库必须配置材质
3. 同仓库 + 同盘点方式 `inventoryOptType` 的 24 小时内未作废盘点单只能存在一张

其中“24 小时限制”使用 `create_time BETWEEN endTime AND startTime` 加 `bill_status != '4'` 判断，等价于把“未作废旧盘点单”当成排他锁。

### 2. 建单时真正落的不是空壳
创建单头后，服务会立刻做两份初始化：
1. `initItemData(...)`
   - 把当前仓库 `wh_item` 中 `item_status in ('0','1','2','3','6','7')` 的单件整批复制到 `wh_item_inventory_bill_item`
   - 初始都记为 `inventory_status = '0'`、`inventory_source = '0'`
2. `initStockBakData(...)`
   - 按 `ware_id + big_class_id + texture_id` 汇总备份件数、金重、货重到 `wh_item_inventory_bill_stock`

所以单件盘点单一创建，就已经把“账面盘点基线”冻结下来了。

### 3. 单头汇总字段
建单后会立即 `updateBillSumColumn(id)`，聚合口径是：
1. `totalNum/totalGoldWeight`
   - 来自 `inventory_source = '0'` 的基线快照
2. `totalNumActual/totalGoldWeightActual`
   - 来自 `inventory_source = '0'` 且 `inventory_status = '1'` 的“已盘到”快照

也就是说，单头汇总不吃盘盈数据，盘盈只在批次级单独统计。

## 批次采集链
### 1. 批次的作用
`saveBatchData(ItemInventoryOptBatchVO)` 承接一次实际盘点采集。它支持：
1. 新建批次
2. 修改已存在批次
3. 按库位盘点时绑定 `localId/areaId`
4. 同一次批次里提交一组 EPC

### 2. 已盘到口径
对本仓账面内 EPC，调用 `updateItemStatus(...)`：
1. 只更新当前盘点单中 `inventory_status = '0'`、`inventory_source = '0'` 的记录
2. 把它们改成 `inventory_status = '1'`
3. 绑定当前 `batch_id`
4. 若当前盘点方式是“按库位”，还会附带 `local_id = 当前批次库位` 过滤

这表示“本来就在账面库里的单件，被本次盘到了”。

### 3. 盘盈口径
对提交 EPC 中“不在当前盘点单快照里，但系统里真实存在”的单件，调用 `insertItemMore(...)`：
1. 新插入到 `wh_item_inventory_bill_item`
2. `inventory_status = '1'`
3. `inventory_source = '1'`
4. 仅收“真实仓库不等于当前盘点仓库”的 EPC

所以这里的“盘盈”更准确说是“当前盘点单视角下的额外发现单件”，并不一定是系统绝对意义上的无中生有。

### 4. 批次汇总
`updateBatchSumColumn(batchId)` 聚合口径分成两块：
1. `totalNumActual/totalGoldWeightActual`
   - 统计 `inventory_source = '0' AND inventory_status = '1'`
2. `totalNumMore/totalGoldWeightMore`
   - 统计 `inventory_source = '1'`

所以批次层同时保留“盘到多少”和“盘盈多少”两套数。

## 查询与报表口径
### 1. 待盘清单
`pageWaitItem(...)` 分两条路径：
1. 有 `billId`
   - 从 `wh_item_inventory_bill_item` 里查当前盘点单尚未盘到的数据
2. 只有 `wareId`
   - 直接从 `wh_item` 查某仓当前可盘单件

### 2. EPC 回查
`getItemInventoryOptItemByEpc(...)` 先查当前盘点单里已存在的 EPC，再补查系统真实 `wh_item`，目的是让前端扫码时既能识别“本单已采过”，也能识别“这是盘盈条码”。

### 3. 总盘点列表
`pageInventory(...)` 不是只查单件盘点单，它会 `UNION`：
1. `wh_item_inventory_bill`
2. `wh_inventory_weighing_bill`

所以前端“盘点列表”其实是单件盘点和盘点称重单共用的混合入口。

## 审核、反审、作废
### 1. 审核
`approved(Long id)` 目前只做：
1. 校验单据存在
2. 禁止对 `CONFIRM/DISCARD` 再审核
3. 单头改成 `CONFIRM`
4. 写 `approvedMan/approvedTime`

没有看到根据差异去改 `wh_item`、`wh_sku_stock` 或库存日志。

### 2. 反审
`returnApproved(Long id)` 目前只做：
1. 仅允许 `CONFIRM -> INFIRM`
2. 限制只能反审“当天审核”的单据
3. 清空审核人和审核时间

同样没有看到库存或单件主数据回滚。

### 3. 作废
`discard(Long id)` 只改单头：
1. 禁止重复作废
2. 禁止已审核单据作废
3. 回填 `discardMan/discardTime`

### 4. 作废批次
`discardBatch(Long batchId)` 才是这个模块最像“业务动作”的地方：
1. 先复制该批次原始明细一份
2. 把复制出来的新数据标记 `discard_status = 1`
3. 把旧批次里账面内明细重置回 `batch_id = 0`、`inventory_status = '0'`
4. 把旧批次里的盘盈明细直接软删 `deleted = 1`
5. 批次头改为已作废
6. 重算单头汇总

它的本质是“撤销一次采集结果”，不是删整张盘点单。

## 当前结论
1. 单件盘点单操作的核心不是库存过账，而是冻结盘点基线、采集盘点结果、按批次维护差异。
2. 审核/反审目前只改单头状态，没有看到把差异正式落回 `wh_item`、`wh_sku_stock` 或库存日志的实现。
3. 盘盈在这里是“当前盘点单视角下的额外 EPC”，口径依赖“是否已在当前盘点单快照里”，不是纯粹的无源新增。
4. 前端盘点列表实际把单件盘点单和盘点称重单合并展示，不能把这个页面结果直接等同于单件盘点单主表。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `approved` | 单件盘点单操作 | 审核当前只改单头状态，没看到依据差异正式回写 `wh_item`、库存或日志 | 待确认 | 确认差异生效是否在别的模块/定时任务/人工流程中执行 |
| ⚠️ | `returnApproved` | 单件盘点单操作 | 反审只允许撤当天审核单据，且没有跨日权限配置分支 | 待确认 | 确认业务上是否明确禁止隔日反审 |
| ⚠️ | `createInventoryBill` | 单件盘点单操作 | 24 小时排他校验用的是 `bill_status != '4'`，对状态码含义存在硬编码依赖 | 待确认 | 确认 `4` 是否稳定等于作废态 |
| ⚠️ | `deleteItemInventoryBill` | 单件盘点单操作 | 通用 CRUD 删除只删主表，未见批次、明细、库存备份级联清理 | 待确认 | 确认该删除接口是否已废弃或仅供代码生成残留 |
