# 维修入库单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修入库单对应维修链里的“返厂维修回收入库”执行单。它不是简单把货收回来，而是同时承担四类动作：
1. 把维修出库单的待入库数量核销回来
2. 把维修单明细的入库/工费/新件信息累计回写
3. 把返厂单件或成品重新入库
4. 把维修产生的材质/工费入到供应商钱包

所以它本质上是“维修出库回收入库 + 供应商结算 + 维修宿主链累计回写”的复合执行单。

## 关键入口
- Controller: [MaintainInBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintaininbill/MaintainInBillController.java)
- Service: [MaintainInBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintaininbill/MaintainInBillServiceImpl.java)
- 审核业务层: [MaintainInBillBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainInBillBusiness.java)
- 建单辅助: [MaintainInBillCreateBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainInBillCreateBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_maintain_in_bill` | 主表 | 维修入库单头，保存审核态、供应商、材质、合计工费等 |
| `wh_maintain_in_bill_detail` | 明细表 | 维修入库明细，挂维修单明细与维修出库单明细 |
| `wh_maintain_bill` | 宿主表 | 审核/反审时重算单头维修进度 |
| `wh_maintain_bill_detail` | 宿主明细 | 回写 `inboundNum/inNum/repareFee/newItem.../replyStatus` |
| `wh_maintain_out_bill` | 来源主表 | 回写维修出库累计入库数 |
| `wh_maintain_out_bill_detail` | 来源明细 | 回写 `inNum`，并校验剩余待入库量 |
| `wh_item` | 单件主档 | 单件维修入库审核时改回在库状态；反审时恢复维修态 |
| `wh_item_log` | 单件日志 | 记录维修入库审核/反审的单件轨迹 |
| `wh_sku_stock` | 成品库存 | 单件/SKU 维修入库都会回写库存 |
| `wh_wallet` / `wh_wallet_log` | 供应商钱包 | 按材质重量和工费金额入账或回滚 |

## 建单逻辑
### 1. 合计值重算
`computeSummary(reqVO)` 会逐条重算：
1. `maintainCmtFee = maintainGramCmtFee * goldWeight + maintainItemCmtFee * num`
2. `generalCmtFee = costGramCmtFee * goldWeight + costItemCmtFee * num`
3. `totalCmtFee = totalMaintainCmtFee + totalGeneralCmtFee`

并汇总：
1. `totalNum`
2. `totalGoldWeight`
3. `totalGrossWeight`
4. `totalWeight`
5. `totalMaintainCmtFee`
6. `totalGeneralCmtFee`
7. `totalCmtFee`

这说明维修入库单的工费口径不是前端直接给定，而是后端按克工费/件工费重新算出来。

### 2. 来源校验
`setDetailRelateData(...)` 对每条明细都会同时校验三组来源：
1. 维修单明细必须存在，且所属维修单已审核
2. 维修单明细不能已经是 `COMPLETED/完结`
3. 维修出库单明细必须存在，且 `num - inNum > 0`

同时还会保证：
1. 维修单大类/材质与入库单头一致
2. 维修出库单供应商与入库单头一致
3. 同一张维修出库明细不能在一张入库单里重复引用

### 3. 单件分支与成品分支
入库明细不允许混单：
1. 有 `itemId` 的走单件维修入库，单头 `bizType = 9001`
2. 没有 `itemId` 的走成品维修入库，单头 `bizType = 9002`

代码里通过 `itemCount/goodsCount` 强制“单件和成品不能同时入库”。

### 4. 建单即回写维修出库挂接
`updateRelateOutBill(detailDOList)` 在保存明细后，会把来源维修出库明细的：
1. `pushMaintainInBillDetailId = 当前维修入库明细ID`
2. 对应维修出库单头 `totalPushCount`

也就是说，维修入库单草稿建立后，就已经在维修出库侧留下“已下推”痕迹。

## 审核链
审核入口不是 Service 自己做完，而是：
1. `MaintainInBillBusiness.confirm(id)`
2. `ConfirmHandle.handle()`

执行顺序固定为：
1. `preCheck()`
2. `itemHandle()`
3. `goodsHandle()`
4. `settleHandle()`
5. `relateBillHandle()`
6. `billHandle()`

### 1. 单件返仓
`itemHandle()` 对单件维修入库会同时做三件事：
1. 更新 `wh_item`：把单件改回 `ON_WARE`，锁定状态改成未锁，仓位切回当前维修入库单头仓库/库位，并把金重/毛重/货重改成维修后值
2. 写 `wh_item_log`
3. 调 `skuStockService.increaseStock(...)` 增加单件库存

所以单件维修入库审核的真实含义是“维修态单件重新回到在库态”。

### 2. 成品库存回写
`goodsHandle()` 会统一调用：
`skuStockService.updateGoodsStock(goodsStockList, false, IN, BizTypeEnum.getEnum(billDO.getBizType()))`

不论是单件维修还是成品维修，审核都把维修后的货重新计入成品库存口径。

### 3. 供应商钱包入账
`settleHandle()` 会调用：
`createWalletBaseDTO(DEPOSIT, CONFIRM)`

钱包数据来自 `ConfirmData.getWalletData()`，固定拆成两笔：
1. 材质钱包：按明细 `goldWeight` 合计
2. 现金钱包：按明细 `cmtFee` 合计，四舍五入到整数

所以维修入库单审核时，供应商会同时拿到“维修后金重”和“工费金额”两条钱包入账。

### 4. 维修单累计回写
`addNumToMaintainBill()` 会把来源维修单明细回写为：
1. `inboundNum += num`
2. `inNum += num`
3. `repareFee += maintainCmtFee`
4. 更新 `newGoldWeight/newGrossWeight/newWeight`
5. 若是单件，还会回写 `newItemId/newItemNo`
6. `stepWare/stepLocal` 切到当前维修入库单头仓位
7. `replyStatus = 5`
8. `recordId = 0`

最后再调 `maintainBillService.updateTotalColumn(...)` 重算维修单头进度。

### 5. 维修出库累计回写
`addNumToMaintainOutBill()` 会把来源维修出库明细：
1. `inNum += num`
2. 校验累计入库数不能超过出库数
3. 再重算维修出库单头累计

这意味着维修入库单是维修出库单的回收闭环。

### 6. 单头状态
`billHandle()` 只在最后一步把：
1. `billStatus = 1`
2. `approvedMan/approvedTime`
3. 明细 `billStatus = 1`

因此真正业务动作也都沉在 `MaintainInBillBusiness`，不是单头状态切换本身。

## 反审链
维修入库单反审走：
1. `MaintainInBillBusiness.infirm(id)`
2. `InfirmHandle.handle()`

执行顺序和审核镜像：
1. `preCheck()`
2. `itemHandle()`
3. `goodsHandle()`
4. `settleHandle()`
5. `relateBillHandle()`
6. `billHandle()`

### 1. 单件回滚
反审时 `itemHandle()` 会把单件：
1. 状态改回 `MAINTAIN`
2. 仓位改回 `originShopId/originWareId/originLocalId`
3. 金重/毛重/货重恢复为入库前原值
4. 锁定状态改为锁定
5. 写反审日志
6. `skuStockService.decreaseStock(...)` 回滚单件库存

### 2. 成品库存回滚
`goodsHandle()` 反审调用的是：
`updateGoodsStock(goodsStockList, true, IN, bizType)`

即沿同一业务类型把成品库存整条回滚。

### 3. 供应商钱包回滚
`settleHandle()` 改为：
`createWalletBaseDTO(DEBT, INFIRM)`

即把审核时入到供应商钱包的材质重量和工费整条扣回。

### 4. 维修单/维修出库累计回滚
`subNumToMaintainBill()` 会：
1. `inboundNum/inNum` 扣回
2. `repareFee` 扣回
3. `newGoldWeight/newGrossWeight/newWeight` 清零
4. `newItemId/newItemNo` 清空
5. `replyStatus = 1`
6. `recordId` 回写回来源维修出库明细的 `recordId`

`subNumToMaintainOutBill()` 会：
1. 维修出库明细 `inNum -= num`
2. 再重算维修出库单头累计

## 反审前置限制
`InfirmHandle.preCheck()` 里有几条关键保护：
1. 单头必须是 `billStatus = 1`
2. `totalPushCount` 不能为空，否则视为“已经下推出库单据”，不允许反审
3. 跨日反审需要权限 `wh:maintain-in-bill:infirm`
4. 维修单明细当前流转仓库必须还是本仓
5. 该维修单明细在本次入库审核之后不能又生成新的维修出库单
6. 若是单件，当前单件必须仍在本仓、在库，且重量不能被改过

这说明维修入库单反审风险很高，代码已经把“后续又继续流转”的链路拦住了。

## 作废链
`obsoleteById(id)` 只允许：
1. `INIT`
2. `INFIRM`

作废时会：
1. 单头 `billStatus = 3`
2. 明细 `billStatus = 3`
3. `updateRelateOutBillByObsolete(detailDOList)` 清掉维修出库明细上的 `pushMaintainInBillDetailId`
4. 重算来源维修出库单头 `totalPushCount`

所以维修入库单作废不会改库存和钱包，但会解除“已下推维修出库”的占用关系。

## 当前结论
1. 维修入库单不是普通收货单，而是维修出库链的回收入库执行单。
2. 审核会同时改供应商钱包、单件/成品库存、维修单累计和维修出库累计。
3. 反审不只是改单头，而是把维修后状态、库存、钱包和累计整条镜像回滚。
4. 草稿建单阶段就会先在维修出库明细上挂 `pushMaintainInBillDetailId`，作废时再解除。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `updateRelateOutBill` | 维修入库单 / 维修出库单 | 草稿保存阶段就会把维修出库明细挂上 `pushMaintainInBillDetailId`，不是审核后才挂接 | 待确认 | 确认业务上是否允许“仅建草稿也占用下推关系” |
| ⚠️ | `itemHandle` | 维修入库单 | 单件审核时把 `lockStatus` 直接改成未锁，反审时改成锁定；需要确认是否总能代表维修链真实锁语义 | 待确认 | 核对维修出库、维修详情对单件锁的约定 |
| ⚠️ | `billHandle` | 维修入库单 | 反审时单头改成 `2`，但明细 `billStatus` 仍被写成 `1`，头明细状态看起来不一致 | 待确认 | 确认这是有意保留“明细曾审核”标记，还是写错状态值 |
| ⚠️ | `preCheck` | 维修入库单 | 反审禁止条件依赖 `totalPushCount` 与“审核后是否又生成维修出库单”，后续若有别的下推链路要一起补校验 | 待确认 | 分析维修出库单和维修详情时继续核对后续占用来源 |
