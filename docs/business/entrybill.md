# 单件入库单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
单件入库单对应 `BillTypeEnum.SINGLE_INBOUND`，业务类型至少覆盖：
1. `0801 = SG_IMPORT_IN_STORAGE` 初始导入
2. `0802 = SG_PURCHASE_IN_STORAGE` 采购入库
3. `0803 = SG_SECOND_IN_STORAGE` 二次入库

它不是简单的“单件建档单”，而是一条复合链：
1. 建立单件主数据 `wh_item`
2. 写入单件日志 `wh_item_log`
3. 单件库存入库 `wh_sku_stock`
4. 成品库存出库 `wh_sku_stock`
5. 需求单累计回写
6. 维修单回写

## 关键入口
- Controller: [EntryBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/entrybill/EntryBillController.java)
- Service: [EntryBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/entrybill/EntryBillServiceImpl.java)
- Business: [EntryBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/EntryBillBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_entry_bill` | 主表 | 单件入库单头 |
| `wh_entry_bill_detail` | 明细表 | 单件入库行 |
| `wh_entry_bill_detail_accessories` | 明细辅料 | 单件入库行的辅料快照 |
| `wh_item` | 单件表 | 审核时真正生成单件档案 |
| `wh_item_log` | 单件日志 | 审核/反审写单件进出日志 |
| `wh_item_accessories` | 单件辅料 | 审核后把入库辅料复制到单件 |
| `wh_item_part` | 单件配件 | 反审时一并清理 |
| `wh_sku_stock` | 库存表 | 同时承接“单件库存入 + 成品库存出” |
| `wh_demand_order_detail` | 需求单明细 | 回写 `entryBillNum/entryBillWeight` |
| `wh_demand_order` | 需求单头 | 按明细聚合重算 |
| `wh_maintain_bill_detail` | 维修单明细 | 回写 `itemInNum/newItemNo/newGoldWeight` 等 |
| `wh_maintain_in_bill_detail_log` | 维修入库日志 | 审核时记录维修入库落账痕迹 |

## 建单与更新
### 1. 单头
建单时后端生成 `billNo`，若未传 `localId` 则尝试取仓库默认库位。

### 2. 明细校验
`createEntryBill()/updateEntryBill()` 会统一校验并重算：
1. `goldWeight/weight` 不能为空
2. `tareWeight` 默认 `0.380`
3. `grossWeight` 默认 `weight + tareWeight`
4. 校验 `grossWeight >= weight >= goldWeight`
5. 重新计算工费、标签价、辅料费用
6. 初始导入 `0801` 必须显式带 `epc/itemNo`

### 3. EPC 与条码生成
非初始导入时会按配置自动生成：
1. `itemNo`
2. `epc`

其中 `epcGenRule` 会决定走默认编码还是数字编码；同心环境还会把 `itemNo = epc`。

### 4. 来源挂接
明细里可以挂：
1. `outsourcingOrderNo/outsourcingOrderLineNum`
2. `demandOrderId/demandDetailId`
3. `maintainBillId/maintainBillDetailId`

所以它本质上是“单件库存入库执行单”，不是孤立建档模块。

## 审核链
`confirm(Long id)` 本身只做状态入口检查，真正执行在 `updateProcessResult(..., APPROVE, ...)`。

主顺序是：
1. 单头改成 `CONFIRM`，写 `approvedMan/approvedTime`
2. 读取全部入库明细
3. 校验 EPC 不能已存在于 `wh_item`
4. 逐行组装 `ItemCreateReqVO`
5. 批量插入 `wh_item`
6. 回写明细 `itemId/epc/itemNo/effective=true`
7. 写入 `wh_item_log`
8. 复制辅料到 `wh_item_accessories`
9. 回写需求单累计
10. 回写维修单
11. 先校验成品库存足够
12. 扣减成品库存
13. 增加单件库存

### 1. 单件创建
审核时每条入库明细都会落成一条真实单件：
1. `itemStatus = ON_WARE`
2. 落到单头 `shopId/wareId/localId`
3. 带入 `entryBillNo`
4. 带入委外工艺要求 `processRequire`

### 2. 单件日志
审核写 `ItemLogTypeEnum.IN`，日志口径是“单件入库确认”。

### 3. 库存口径
审核同时做两件事：
1. `skuStockService.updateGoodsStock(..., false, OUT, BizTypeEnum.SG_PURCHASE_IN_STORAGE)`
   - 扣减成品库存
2. `skuStockService.increaseStock(..., billDO.getBizType(), ..., stockList)`
   - 增加单件库存

也就是说单件入库不是净新增库存，而是“成品库存转单件库存”的拆分动作。

### 4. 需求单回写
`compilingDemandOrder(detailList, false)` 会：
1. 按 `demandDetailId` 给 `wh_demand_order_detail.entryBillNum +1`
2. 给 `entryBillWeight + goldWeight`
3. 再通过 `aggregateByDetails(...)` 重算订单头

### 5. 维修单回写
`updateMaintainBill(...)` 会：
1. 增加 `itemInNum`
2. 累加 `newGoldWeight/newGrossWeight/newWeight`
3. 拼接 `newItemNo`
4. 把当前步骤仓位改到本次入库仓库/库位
5. 解锁维修明细
6. 记录维修入库日志
7. 必要时增加维修完成数

## 反审链
`infirm(Long id)` 的主顺序是：
1. 仅允许已审单据反审
2. 校验跨日权限 `ENTRY_BILL_CROSS_DAYS`
3. 校验对应 EPC 当前仍在本仓、状态为 `ON_WARE`、且未锁定
4. 单头改成 `INFIRM`
5. 物理删除这批 `wh_item`
6. 明细回写 `effective=false`
7. 写反向 `wh_item_log`
8. 回滚成品库存扣减
9. 回滚单件库存入库
10. 回滚需求单累计
11. 删除单件辅料/单件配件
12. 回滚维修单累计

### 1. 单件删除
反审不是把单件状态改回某个前置态，而是直接 `deleteItemByIds(...)` 物理删单件。

### 2. 反向日志
反审仍写 `ItemLogTypeEnum.IN`，但 `goldWeight` 取负，并把 `billStatus` 记成 `INFIRM`。

### 3. 库存回滚
1. `updateGoodsStock(..., true, OUT, BizTypeEnum.SG_PURCHASE_IN_STORAGE)` 回补成品库存
2. `decreaseStock(..., billDO.getBizType(), ..., stockList)` 回滚单件库存

### 4. 维修单回滚
`updateMaintainForInfirm(...)` 会：
1. 回退 `itemInNum`
2. 扣减 `newGoldWeight/newGrossWeight/newWeight`
3. 从 `newItemNo` 中移除本次生成的单件编码
4. 恢复 `lastStepWareId/lastStepWare/lastStepLocal`
5. 重新锁定维修明细

## 作废与删除
### 作废
`discard(Long id)` 只允许 `INIT/INFIRM -> DISCARD`，不触发库存或单件回滚。

### 删除
`deleteEntryBill(Long id)` 会物理删除主表，再逐条删除明细；当前看不到状态限制。

## 当前结论
1. 单件入库单的本质不是“建标签”，而是“成品库存拆分成单件库存”的执行链。
2. 审核时真正创建的是 `wh_item`，所以后续单件调整、单件作废都要建立在这条链之上。
3. 需求单和维修单都不是被动展示字段，而是审核/反审都会实时累计与回滚的宿主链。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteEntryBill` | 单件入库单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | `confirm` | 单件入库单 | 只拦重复审核，未显式限制 `INIT/INFIRM` 之外状态再次审核 | 待确认 | 确认是否应统一改成 `BillStatusEnum.confirmCheck(...)` |
| ⚠️ | `confirm/infirm` | 单件入库单 | 成品库存更新统一传 `BizTypeEnum.SG_PURCHASE_IN_STORAGE`，即使真实单据可能是 `0801/0803` | 待确认 | 确认库存日志是否会丢真实业务类型 |
| ⚠️ | `infirm` | 单件入库单 / 单件表 | 反审直接物理删除 `wh_item`，不是状态回滚 | 待确认 | 确认历史追溯是否只依赖 `wh_item_log` |
| ⚠️ | `confirm` / `updateProcessResult` | 单件入库单 / BPM | 审核入口直接手动调 `updateProcessResult(...APPROVE...)`，看起来没有真实 BPM 审批等待 | 待确认 | 确认该模块是否已经绕过 BPM 直接本地审核 |
