# 维修单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修单对应“维修宿主链”。它不是单纯的登记单，也不是单一审核流，而是后续维修入库、维修出库、单件入库、单件调整、客户退货、成品退厂登记等模块持续回写的中心单据。

当前代码里至少同时维护三组状态：
1. `billStatus`：单头审核态，`0/1/2/3 = INIT/CONFIRM/INFIRM/DISCARD`
2. `replyStatus`：维修流转态，单头注释可见 `0=初始，1=维修中，2=部分到柜，3=已到柜，4=已完结`
3. `transferStatus`：是否已执行“一键调拨”

所以它本质上是“维修任务宿主 + 进度聚合器 + 回写锚点”三合一模块。

## 关键入口
- Controller: [MaintainBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/MaintainBillController.java)
- Service: [MaintainBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainbill/MaintainBillServiceImpl.java)
- 确认业务层: [MaintainBillConfirmBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainBillConfirmBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_maintain_bill` | 主表 | 维修单头，保存审核态、维修态、调拨态与汇总字段 |
| `wh_maintain_bill_detail` | 明细表 | 单件/SKU 维修明细，保存流转仓位、来源挂接、计数累计与新旧件信息 |
| `wh_maintain_bill_settle` | 结算明细 | 客户维修确认时的钱包/结价依据 |
| `wh_item` | 单件主档 | 审核/反审时挂接或解除维修单归属 |
| `wh_item_log` | 单件日志 | 维修相关的单件轨迹 |
| `wh_return_detail` | 客户退货明细 | 客户维修单审核/反审时累计 `maintainNum` |
| `wh_sku_stock` | 成品库存 | 柜台维修、SKU 维修等分支会回写成品库存 |

## 建单逻辑
### 1. 单头初始化
`createBill(...)` 会直接在维修单头落这些关键默认值：
1. `billNo = nextBillNo(bizType, bizDate)`
2. `billName = 维修单`
3. `billStatus = 0`
4. `areaId/localId` 取维修仓默认库区/库位
5. `businessStaffId/businessAreaId` 优先取当前登录人的业务员/分区

### 2. 明细模式
`detailType` 被强制限制为：
1. `1 = 单件维修`
2. `2 = 成品/SKU 维修`

这决定了后续校验和回写分支。单件维修会强校验 `itemNo/itemId`、单件状态、来源仓位等；成品维修则以 `skuId/skuNo` 为主。

### 3. 客户校验
`checkCustomer(...)` 不是所有维修单都校验客户：
1. 柜台维修单可不校验客户
2. 客户成品维修单不校验“最后一次退货客户”
3. 其余单件维修会反查 `ReturnDetailMapper.lastByItemNos(...)`，要求维修客户与最近一次退货客户一致

这说明维修单和客户退货链是强绑定的。

### 4. 汇总字段
`summaryTotal(...)` 会回写：
1. `totalNum`
2. `totalGoldWeight`
3. `totalGrossWeight`
4. `totalWeight`
5. `totalCmtFee`

这些字段不是前端可信输入，而是后端按明细重算。

## 审核链
维修单真正的审核不是 `MaintainBillServiceImpl.confirm(...)` 自己完成，而是：
1. `MaintainBillConfirmBusiness.confirm(id)`
2. 先跑 `ConfirmDataHandle.handle()`
3. 再调用 `maintainBillService.confirm(id)` 改单头状态

### 1. 单头状态切换
`maintainBillService.confirm(id)` 只负责把单头推进为：
1. `billStatus = 1`
2. `approvedMan/approvedTime`
3. `replyStatus = 1`
4. `transferStatus = 0`
5. 明细 `replyStatus` 批量推进到 `1`

也就是说，真正业务动作在 `MaintainBillConfirmBusiness`。

### 2. 确认业务层的真实动作
`ConfirmDataHandle` 里可以看到维修单审核会按分支触发这些动作：
1. `itemDoUpdate()`：把关联单件挂上 `maintainBillId / maintainBillDetailId / maintainBillNo / maintainBillDetailReplyStatus`
2. `itemLog()`：预留维修单件日志写入
3. `returnDetailHandle()`：客户维修单回写客户退货明细 `maintainNum`
4. `settleHandle()`：客户维修单按结算明细回写钱包，并把 `CLOSING_PRICE(02)` 单独做反向冲回
5. `goodsHandle()`：成品/SKU 维修分支回写成品库存
6. `itemHandle()`：单件维修分支会改单件仓位/状态并同步单件库存、成品库存差额

从代码结构看，维修单审核不是单一动作，而是“来源挂接 + 钱包 + 单件 + 成品库存”的复合链。

### 3. 钱包口径
`settleHandle()` 明确做两段钱包动作：
1. `walletService.doChangeWallet(createWalletBaseDTO(DEPOSIT))`
2. `walletService.doChangeWallet(createCloseWalletBaseDTO(DEBT))`

含义是：
1. 维修确认主账先按结算明细入客户钱包
2. 其中属于 `CLOSING_PRICE(02)` 的部分，再按相反方向单独冲回

这和客户来料、客户退货里的结价逻辑是一致的宿主单分支。

### 4. 客户退货回写
`updateReturnDetailNum(...)` 会按维修明细累计/回滚 `wh_return_detail.maintainNum`，并校验：
1. 维修数量必须大于 0
2. 维修累计不能超过客户退货数量

所以客户退货单是维修单的核心来源池之一。

## 反审链
维修单反审走：
1. `MaintainBillConfirmBusiness.infirm(id)`
2. 先执行 `InfirmDataHandle.handle()`
3. 再 `maintainBillService.infirm(id)`

### 1. 单头反审状态
`maintainBillService.infirm(id)` 会把：
1. `billStatus = 2`
2. `infirmMan/infirmTime`
3. 清空 `approvedMan/approvedTime`
4. `replyStatus = 0`
5. 明细 `replyStatus` 批量回到 `0`

### 2. 业务回滚
`InfirmDataHandle` 能看到的核心回滚是：
1. `itemDoUpdate()`：把单件上的 `maintainBillId/DetailId/No/ReplyStatus` 清空
2. `returnDetailHandle()`：把客户退货明细 `maintainNum` 回滚
3. 若前面分支产生了钱包/库存动作，则按相反方向回滚
4. `settleHandle()` 反向组合为 `DEBT + 结价 DEPOSIT`

所以反审不是只改单头，而是解除维修归属并回滚来源累计。

## 一键调拨 / 到柜链
### 1. 一键调拨
`transferToCounter(id, targetWareId)` 会按明细类型自动分流：
1. 单件明细：生成 [TransferBill](/D:/ws/code/wms-api/docs/business/transferbill.md) 并立刻审核
2. 成品明细：生成 [GoodsTransferBill](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) 并立刻审核

完成后把维修单头 `transferStatus = 1`。

这说明维修单不是最终到柜执行单，而是可以派生调拨执行单。

### 2. 设置到柜
`setToCounter(detailIds)` 不是正式库存动作，只是把明细 `toCounter = true`。它唯一显式限制是：
1. 单头 `replyStatus != 4`

因此“到柜”在这里更像流程标记，不是直接改库存。

## 进度聚合逻辑
`updateTotalColumn(ids)` 会先重算汇总，再按累计数字推进 `replyStatus`：
1. 客户维修单：`totalSettleNum + totalCounterNum >= totalNum` 时，单头 `replyStatus = 4`
2. 非客户维修单：`totalFinishNum >= totalNum` 时，单头 `replyStatus = 4`
3. `totalFinishNum >= totalNum` 但未满足完结条件时，单头 `replyStatus = 3`
4. `totalFinishNum > 0` 时，单头 `replyStatus = 2`

这说明维修单真正的“完成”不是由单头审核决定，而是由后续入库、结算、上柜等累计量聚合出来。

## 查询层含义
`getMaintainBillVO(id)` 除了回主表和明细，还会额外补：
1. 维修原因字典
2. 仓库/SKU 信息
3. `MaintainBillUtil.lineNumQrCode(...)`
4. 最近一次维修入库明细的 `maintainItemCmtFee/maintainGramCmtFee`

所以维修单详情页本身就已经承担了“维修跟踪单”视图职责。

## 更新、删除、作废
### 更新
`updateMaintainBill(...)` 允许重写单头和明细，并重新汇总。结合 `confirm()` 的状态校验看，安全状态应该是 `INIT/INFIRM`，但删除/更新侧的保护并不统一。

### 删除
`deleteMaintainBill(id)` 当前只做两件事：
1. 校验主表存在
2. `maintainBillMapper.deleteById(id)`

没看到：
1. 明细级联删除
2. 状态限制
3. 已挂接单件/退货/结算痕迹清理

### 作废
控制器暴露 `invalid`，但当前主服务里最显眼的是审核/反审/更新/删除/到柜/调拨，没有看到和很多业务单一样完整的 `discard()` 主链。维修单的终态更多像由 `replyStatus/finalization` 管理，而不是单一作废态驱动。

## 当前结论
1. 维修单不是单纯建单模块，而是维修域的宿主链和进度聚合器。
2. 审核/反审会同时触及单件挂接、客户退货累计、钱包结算、结价冲回，以及部分分支下的成品/单件库存。
3. `replyStatus` 比 `billStatus` 更接近维修业务真实进度；真正的完结依赖后续维修入库、结算、上柜等累计值。
4. “一键调拨”会直接派生并审核调拨单/成品调拨单，维修单自己不承担最终跨仓执行动作。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteMaintainBill` | 维修单 / 维修明细 | 删除当前只删主表，未见明细级联、状态限制和来源挂接清理 | 待确认 | 确认是否只允许草稿态删除，并补齐明细/挂接清理 |
| ⚠️ | `confirm` / `infirm` | 维修单 | 单头审核态只改 `billStatus`，真正业务动作分散在 `MaintainBillConfirmBusiness`，维护时很容易只改一半链路 | 待确认 | 后续改造必须把 Service 层和 ConfirmBusiness 视为同一条事务链 |
| ⚠️ | `setToCounter` | 维修单 | 当前只拦 `replyStatus = 4`，未见对未审核、已作废等异常状态的显式限制 | 待确认 | 确认“设置到柜”是否应补单头审核态校验 |
| ⚠️ | `updateTotalColumn` | 维修单 | 单头 `replyStatus` 完全依赖累计量聚合，若下游维修入库/结算/上柜回写漏一处，维修单进度会失真 | 待确认 | 后续分析维修入库单、维修出库单、维修详情时重点核对累计字段来源 |
