# 库位调整单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
库位调整单对应 `BillType=11`，主业务类型固定为：
1. `1101 = 库位调整`

它不是简单改一个 `localId` 字段，而是一条“锁单件 -> 审核改库位 -> 调整单件库存库位分布 -> 解锁/反审回滚”的执行链：
1. 建单时先锁住待调整 EPC，防止并发操作
2. 审核时把 `wh_item.local_id` 从原库位改到目标库位
3. 同时通过 `skuStockService.moveStock(...)` 把单件库存从旧库位挪到新库位
4. 反审时再把单件库位和库存分布整条回滚
5. 还带一条外部同步侧链，可把库位调整结果推送到外部系统

## 关键入口
- Controller: [ShelvesBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/shelvesbill/ShelvesBillController.java)
- Service: [ShelvesBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/shelvesbill/ShelvesBillServiceImpl.java)
- Business: [ShelvesBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/ShelvesBillBusiness.java)
- 明细 Mapper: [ShelvesBillDetailMapper.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/mysql/shelvesbilldetail/ShelvesBillDetailMapper.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_shelves_bill` | 主表 | 库位调整单头 |
| `wh_shelves_bill_detail` | 明细表 | 记录 EPC、原库位、目标库位 |
| `wh_item` | 单件表 | 审核/反审时真正修改 `localId` |
| `wh_item_log` | 单件日志 | 建单锁定、审核解锁、作废解锁、反审重新锁定都会记日志 |
| `wh_sku_stock` | 库存表 | 审核/反审时按库位维度移动库存分布 |
| `wh_sync_task` 等同步表 | 同步侧链 | `/sync/{id}` 会把调整结果推外部系统 |

## 建单与改单
### 1. 建单
`createShelvesBill(...)` 的核心流程是：
1. 后端生成 `billNo`
2. 校验仓库存在，并回填 `deptId`
3. 以后端重算明细 `totalWeight/totalGoldWeight/totalNum`
4. 插入单头
5. 逐条校验 EPC 对应单件存在且属于当前仓库
6. 对每个 EPC 执行 `itemService.lockByEpc(...)`
7. 写一条 `wh_item_log`，锁状态记为 `LOCKED`
8. 明细回填 `billId/billNo/destLocalId`
9. 批量写入 `wh_shelves_bill_detail`

所以草稿态库位调整单虽然还没改库存，但已经先把目标单件锁住了。

### 2. 改单
`updateShelvesBill(...)` 只允许：
1. `INIT`
2. `INFIRM`

更新时会重新对比“旧 EPC 集合”和“新 EPC 集合”：
1. 新增 EPC
   - 重新锁定
   - 记锁定日志
   - 新增明细
2. 保留 EPC
   - 更新已有明细
3. 移除 EPC
   - 删除旧明细
   - 释放 EPC 锁
   - 记解锁日志

也就是说，这个模块的改单本质是“重排锁定池 + 重排明细”，不是单纯改表头备注。

## 审核链
`confirm(Long billId)` 的主顺序是：
1. 校验单据存在
2. `BillStatusEnum.confirmCheck(...)` 做状态机校验
3. 明细不能为空
4. 乐观更新单头到 `CONFIRM`
5. 读取全部明细和对应 `wh_item`
6. 为每条单件记一条“解锁”日志
7. 把单件 `localId` 改成单头目标库位 `shelves.localId`
8. 构造 `SkuStockBO.buildShelvesOne(detail.sourLocalId, item)`
9. 调 `skuStockService.moveStock(...)`
10. 批量释放 EPC 锁
11. 批量写单件日志

这里最关键的动作有两条：
1. 单件主数据换库位
2. 库存分布从旧库位搬到新库位

两条一起成立，库位调整才算真正完成。

## 反审链
`infirm(Long billId)` 的主顺序是：
1. 校验单据存在
2. `BillStatusEnum.infirmCheck(...)` 校验只允许已审核单据反审
3. 乐观更新单头到 `INFIRM`
4. 读取全部明细和对应 `wh_item`
5. 重新锁住 EPC
6. 记“锁定”日志
7. 把单件 `localId` 改回 `detail.sourLocalId`
8. 构造 `SkuStockBO.buildShelvesOne(detail.destLocalId, item)`
9. 再调一次 `skuStockService.moveStock(...)`

和审核相比，反审的语义是：
1. 库位回退
2. 库存分布回退
3. 单件重新回到“被本单锁定”的状态，方便继续修改或作废

## 作废与删除
### 1. 作废
`discard(Long billId)` 只允许通过 `BillStatusEnum.obsoleteCheck(...)` 的单据作废。动作是：
1. 单头改 `DISCARD`
2. 读取全部明细
3. 释放所有 EPC 锁
4. 写解锁日志

作废不改 `wh_item.localId`，也不调库存，因为它只允许发生在未完成执行的状态。

### 2. 删除
`deleteShelvesBill(Long id)` 当前只做：
1. 校验主表存在
2. 物理删主表

没看到：
1. 明细级联删除
2. EPC 解锁
3. 状态限制

所以它更像代码生成残留接口，不像完整业务删除链。

## 查询与同步侧链
### 1. 查询
`ShelvesBillBusiness` 负责把主表和明细补齐为 VO：
1. 页列表按登录人仓库权限过滤
2. 详情页会再补单件的 `skuNo/skuName/imageUrl/modelNo/specs/num`
3. 明细导出也会把原库位/目标库位编码补全

### 2. 外部同步
`/sync/{id}` 调的是 `syncTaskService.syncLocalChange(id)`：
1. 先取库位调整单 VO
2. 创建同步任务
3. 组装明细和单件信息推外部系统
4. 回写 `syncId/syncStatus`

所以库位调整单除了内部库存链，还带一条外部系统同步链。

## 当前结论
1. 库位调整单的本质不是“改单件库位字段”，而是“锁单件 + 审核时改单件库位 + 按库位 moveStock + 解锁”的执行单。
2. 审核和反审都是完整双向链，都会同时影响 `wh_item`、`wh_sku_stock` 和 `wh_item_log`。
3. 作废只负责释放锁，不回滚库位或库存，说明状态机默认要求只有未执行完成的单据才能作废。
4. 该模块还有独立的外部同步分支，改主链时不能只盯内部库存逻辑。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteShelvesBill` | 库位调整单 | 删除只删主表，未见明细级联、EPC 解锁和状态限制 | 待确认 | 确认该接口是否已废弃，或补完整删除保护链 |
| ⚠️ | `updateShelvesBill` | 库位调整单 | 更新时先算出 `shelves.setTotal...`，但真正 `updateById` 用的是 `updateObj`，汇总字段可能没写回 | 待确认 | 确认单头合计是否会被更新请求覆盖丢失 |
| ⚠️ | `discard` | 库位调整单 | 作废分支里 `itemMap` 以 `itemId` 为 key，却按 `d.getEpc()` 取值，存在取空风险 | 待确认 | 修正为按 `itemId` 取 `ItemDO`，避免解锁日志异常 |
| ⚠️ | `ShelvesBillDetailMapper.selectList` | 库位调整单 | 导出查询最后直接 `return selectList()`，前面构造的过滤条件没有真正使用 | 待确认 | 修正为 `return selectList(wrapperX)`，避免导出全表 |
