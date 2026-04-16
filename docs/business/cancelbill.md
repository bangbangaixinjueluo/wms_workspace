# 单件作废单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
单件作废单对应 `BillTypeEnum.SINGLE_DISCARD`，业务类型固定为：
1. `1501 = SG_DISCARD`

它不是删除单件，而是把已有单件状态改成 `DISCARD`，同时联动两条库存链：
1. 单件库存出库
2. 成品库存回收

反审时再把这两条链完整回滚。

## 关键入口
- Controller: [CancelBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/cancelbill/CancelBillController.java)
- Service: [CancelBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/cancelbill/CancelBillServiceImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_cancel_bill` | 主表 | 单件作废单头 |
| `wh_cancel_bill_detail` | 明细表 | 单件作废行 |
| `wh_item` | 单件表 | 审核时把单件状态改为 `DISCARD`，反审改回 `ON_WARE` |
| `wh_item_log` | 单件日志 | 审核、反审都写作废/回滚日志 |
| `wh_sku_stock` | 库存表 | 同时承接单件库存出入与成品库存进出 |

## 建单与修改
### 1. 单头
建单时后端生成 `billNo`，并重新汇总：
1. `totalNum`
2. `totalGoldWeight`

### 2. 修改
`updateCancelBill()` 只允许 `INIT/INFIRM` 修改；更新时也会重新按明细重算件数与金重。

### 3. 删除
`deleteCancelBill()` 直接物理删除主表和全部明细，当前看不到状态限制。

## 审批链
### 1. 提交
`confirm(Long id)` 的实际含义是“提交审批”：
1. 发起 BPM
2. `processResult = PROCESS`
3. `billStatus = CONFIRMING`

### 2. 审批通过回写
`updateProcessResult(..., APPROVE, ...)` 才是真正落业务的入口。

主顺序是：
1. 单头改成 `CONFIRM`
2. 逐条校验 EPC 对应单件必须存在且状态为 `ON_WARE`
3. 把单件状态改成 `DISCARD`
4. 写单件日志
5. 回收成品库存
6. 扣减单件库存

## 审核口径
### 1. 单件状态
审批通过不是删单件，而是：
1. `wh_item.itemStatus = DISCARD`

### 2. 单件日志
审核会写一条 `OUT` 日志：
1. `itemStatus = DISCARD`
2. `billStatus = CONFIRM`
3. `goldWeight` 记负值

这条日志口径表示“单件从在库链退出，进入作废态”。

### 3. 库存口径
审核时：
1. `updateGoodsStock(..., false, IN, BizTypeEnum.getEnum(cancelBillExist.getBizType()))`
   - 成品库存入库
2. `decreaseStock(..., cancelBillExist.getBizType(), ..., stockList)`
   - 单件库存出库

也就是说单件作废并不是纯损失，而是把单件库存回收到成品库存池。

## 反审链
`infirm(Long id)` 只允许已审单据反审，并额外校验跨日权限 `PERMIT_CANCEL_BILL_CROSS_DAYS`。

主顺序是：
1. 单头改成 `INFIRM`
2. 校验对应单件当前必须已经是 `DISCARD`
3. 把单件状态改回 `ON_WARE`
4. 写回滚日志
5. 扣减成品库存
6. 增加单件库存

### 1. 反向日志
反审也写 `OUT` 日志，但：
1. `billStatus = INFIRM`
2. `itemStatus = ON_WARE`
3. 仍然用负金重

### 2. 库存回滚
反审时：
1. `updateGoodsStock(..., false, OUT, BizTypeEnum.getEnum(cancelBillDO.getBizType()))`
   - 把审核时回收的成品库存再扣回去
2. `increaseStock(..., cancelBillDO.getBizType(), ..., stockList)`
   - 把单件库存补回

## 作废
`discard(Long id)` 允许：
1. `INIT`
2. `INFIRM`

进入 `DISCARD`，不触发库存动作。

## 当前结论
1. 单件作废单不是物理删除单件，而是“改单件状态 + 单件库存出库 + 成品库存回收”的状态与库存复合链。
2. 反审会把单件从 `DISCARD` 拉回 `ON_WARE`，并把库存整条恢复。
3. 至此单件链已经补出三段：单件入库负责创建，单件调整负责修改，单件作废负责退出。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteCancelBill` | 单件作废单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认是否只允许草稿态删除 |
| ⚠️ | `confirm` | 单件作废单 | 方法名叫 `confirm`，实际语义是“提交 BPM 审批” | 待确认 | 确认前后端命名是否已统一理解 |
| ⚠️ | `infirm` | 单件作废单 | 跨日权限报错文案写成了“产品包跨日审核反审权限” | 待确认 | 确认是否应改成单件作废模块自己的文案 |
| ⚠️ | `infirm` | 单件作废单 | 反审要求单件状态必须是 `DISCARD`，异常提示写成“必须是核销状态反审核” | 待确认 | 确认提示文案是否复制错误 |
