# 单件明细
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
单件明细模块不是独立审批单，而是整个单件域的“主档 + 查询 + 运维入口”：
1. 主表是 `wh_item`
2. 追踪表是 `wh_item_log`
3. 为单件入库、单件调整、单件作废、库位调整、退货、转料等执行链提供共同落点
4. 同时承接查询、导出、财务视图、标签复核、客锁释放、工费口径查询等操作

所以它更像单件域的读写中心，而不是一张业务单据。

## 关键入口
- Controller: [ItemController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/item/ItemController.java)
- Service: [ItemServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/item/ItemServiceImpl.java)
- Mapper: [ItemMapper.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/mysql/item/ItemMapper.java)
- Business: [ItemBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/ItemBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_item` | 主表 | 单件主档，承接库存位置、状态、费用、订单来源、销售来源等全量字段 |
| `wh_item_log` | 日志表 | 记录单件的进出、锁定、作废、复核、客锁释放等动作痕迹 |
| `wh_item_accessories` | 辅料表 | 查询和导出时会补到单件视图里 |
| `wh_item_tid_record` | TID 记录 | 标签复核时会维护 TID 历史 |

## 主档定位
`wh_item` 是前面多条单件执行链最终共同修改的落点：
1. 单件入库单创建真实单件
2. 单件调整单直接覆写 `wh_item`
3. 单件作废单改 `item_status`
4. 库位调整单改 `local_id`
5. 调拨、退货、转料会改仓库、网点、状态、锁定等字段

因此分析“单件明细”不能只看查询接口，还要把它当成单件域事实表。

## 查询链
### 1. 普通分页
`getItemPage(ItemPageReqVO)` 的核心逻辑是：
1. 先按登录人仓库权限裁剪 `wareIds`
2. 支持大量字段过滤，包括：
   - `itemNo/epc`
   - `skuNo/skuId/modelNo/specs`
   - 来源单号：`entryBillNo/demandOrderNo/outsourcingOrderNo`
   - 状态：`itemStatus/lockStatus/checkStatus`
   - 库位：`wareId/localId/localIds`
   - 客户、业务员、供应商、营销名等
3. 再回填：
   - 仓库名
   - SKU 图和营销名
   - 最近一条单件日志的 `latestBillNo/latestLogType/latestBillType/latestTime`
   - 在库天数
   - 单件辅料清单
4. 还会额外返回 summary 汇总：
   - `goldWeight`
   - `cmtFee`
   - `weight`
   - `grossWeight`
   - `tareWeight`

所以它不是简单 `select * from wh_item`，而是一个聚合查询视图。

### 2. 导出
`exportItemExcel(...)` 延续普通分页过滤口径，再补：
1. 仓库名
2. 库位编码/名称
3. 大类名
4. 材质名
5. 营销名

### 3. 财务即时库存视图
`financePage/financeExport` 走的是另一套财务导向查询：
1. 先按仓库权限收敛
2. 支持按镶石等财务口径补过滤
3. 结果会补辅料
4. 导出时还会把：
   - `itemStatus`
   - `lockStatus`
   - `lastActiveLogType`
   - `lastActiveBillType`
   统一翻译成可读文本

这说明“单件明细”下面实际挂了至少两套视图：
1. 业务单件列表
2. 财务即时库存列表

## 锁定与状态能力
### 1. EPC 锁
`lockByEpc(String epc)` 只允许锁：
1. `lockStatus = UN_LOCK`
2. `itemStatus in (ON_WARE, DISPLAY)`

`unlockByEpcList(...)` 则是直接批量解锁，不额外限制状态。

这套能力被：
1. 产品包
2. 客留存
3. 库位调整单
4. 单件调整单
5. 其他需要占用单件的流程
共同复用。

### 2. 状态批量更新
`updateItemStatusByEpcs(...)` 支持按 EPC 批量改：
1. `itemStatus`
2. `lockStatus`
3. `wareId`

并强制要求更新条数等于 EPC 数量，说明它被当成需要强一致的执行入口。

### 3. 仓库变更
`wareChangeAndUnLock(...)` 可以同时改：
1. `shopId`
2. `wareId`
3. `localId`
4. `lockStatus`
5. `itemStatus`

这是单件跨仓/跨店执行链的重要公共能力。

## 单件复核与客锁
### 1. 标签复核
`itemCheck(ItemCheckReqVO)` 的核心动作是：
1. 单件必须存在
2. 单件不能处于锁定状态
3. 复核毛重必须 `>= 货重 + 皮重`
4. 回写：
   - `checkGrossWeight`
   - `checkStatus`
   - `checkMan`
   - `checkTime`
5. 若 TID 变更，还会维护 TID 历史
6. 写一条 `wh_item_log`

`ItemBusiness.itemCheck(...)` 还会顺带回写单件入库单明细复核状态，并在整单全部复核通过后推进入库单头的复核状态。

所以标签复核不是纯单件自循环，它还会反推单件入库链。

### 2. 客锁释放
`ItemBusiness.releaseCustomerLock/batchReleaseCustomerLock` 会：
1. 校验单件确实存在客锁
2. 清空 `customerLock`
3. 写 `wh_item_log`

这说明客锁是落在单件主档上的占用状态，不是单独一张关系表独占维护。

## 当前结论
1. 单件明细模块的本质是 `wh_item + wh_item_log` 这组主数据/事件表，不是单独审批模块。
2. 它既是前面所有单件执行单的共同结果落点，也是业务查询、财务查询、导出和运维动作的统一入口。
3. “最近动作、在库天数、辅料、财务视图、客锁、标签复核”这些都不是外围附属功能，而是单件域对外展示口径的一部分。
4. 后续改任何单件执行链，如果改到了 `wh_item` 字段语义或 `wh_item_log` 写法，都要回头看这个模块的查询口径会不会被带歪。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `unlockByEpcList` | 单件明细 | 批量解锁不校验当前业务状态，可能把别的链路锁住的单件直接解开 | 待确认 | 确认是否所有调用方都能保证只解自己持有的锁 |
| ⚠️ | `ItemMapper.selectList(ItemExportReqVO)` | 单件明细 | 导出口径里 `partsFee` 过滤写成了 `getPartsPrice`，字段映射看起来不一致 | 待确认 | 确认这里是否应改成 `getPartsFee` |
| ⚠️ | `getItemByItemNo` | 单件明细 | 方法名按条码查，但实现是 `epc = itemNo OR item_no = itemNo`，语义比名字更宽 | 待确认 | 确认调用方是否依赖这种“EPC/条码混查”行为 |
| ⚠️ | `releaseCustomerLock` | 单件明细 / 单件日志 | 同一流程里既 `createItemLog(...)` 又手工 `save(itemLog)`，客锁释放日志可能重复 | 待确认 | 确认是否确实会落两条同义日志 |
