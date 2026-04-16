# 成品入库单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
成品入库单对应 `BillTypeEnum.GOODS_INBOUND(04)`，实际不只覆盖表注释里的 `0401-成品入库`，代码里还把另外两种业务一起放进同一套主表和服务：
1. `0401 = G_PURCHASE_IN_STORAGE` 成品入库
2. `0402 = G_SUPPLIER_RETURN` 供应商退料
3. `0403 = G_CONVERT_IN_STORAGE` 转成品入库

它不是单纯库存单。审核/反审时至少会同时影响两条链：
1. 成品库存
2. 供应商钱包

其中 `0403` 转成品入库还会额外影响半成品回流量和供应商领料台账。

## 关键入口
- Controller: [GoodsInboundBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/goodsinboundbill/GoodsInboundBillController.java)
- Service: [GoodsInboundBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsinboundbill/GoodsInboundBillServiceImpl.java)
- 导入入口: [GoodsInboundBillImportFromU10Business.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/GoodsInboundBillImportFromU10Business.java)
- API 查询入口: [GoodsInboundApiImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/api/goods/GoodsInboundApiImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_goods_inbound_bill` | 主表 | 成品入库单头 |
| `wh_goods_inbound_bill_detail` | 明细表 | SKU、件数、金重、工费等 |
| `wh_goods_inbound_accessories` | 附件明细 | 镶石/辅料等附属成本结构 |
| `wh_goods_inbound_part` | 配件明细 | 配件明细结构 |
| `wh_goods_inbound_semis_record` | 半成品记录 | 转成品入库、退料回流时的半成品用量记录 |
| `wh_sku_stock` | 成品库存 | 审核/反审时增减 |
| `wh_wallet` | 供应商钱包 | 记录材质钱包和现金钱包 |
| `wh_wallet_log` | 钱包日志 | 记录供应商钱包过账 |

## 建单逻辑
### 1. 单头快照
建单时会先做两类快照：
1. `checkInfo()` 校验 `textureId`、`bigClassId` 必须存在
2. `setStockWeightAndMoney()` 通过供应商账户读取当前钱包余额，回填：
   - `stockWeightNow`
   - `stockMoneyNow`

这意味着单头里的“截止本单欠料/欠款”不是前端传值，而是建单时从供应商钱包实时读取的快照。

### 2. 明细工费重算
`convertDetails(...)` 会重算每行 `cmtFee`：
1. 普通成品入库 / 供应商退料：
   - `cmtFee = goldWeight * costGramCmtFee + num * costItemCmtFee`
2. 转成品入库 / 退料回流：
   - `cmtFee = num * ropeWeavingFee`

### 3. 转成品特殊汇总
`isConvertOrBack()` 把 `0402/0403` 当成特殊分支。此时：
1. 单头 `totalCmtFee` 还会叠加半成品记录里的 `pickCmtFee + supplyCmtFee`
2. 单头 `totalGoldWeight` 必须等于半成品记录里“领料 + 供料”的实际用量合计

所以 `0403` 不是“普通入库 + 多一张记录表”，而是工费和金重校验口径都换了。

## 审核链
`confirm()` 的主顺序是：
1. 只允许 `INIT/INFIRM`
2. 单头状态改成 `CONFIRM`
3. 查询入库明细，不能为空
4. `giveSemisTransfer(..., false)` 回写半成品回流量
5. `skuStockService.updateGoodsStock(..., IN, G_PURCHASE_IN_STORAGE)` 增加成品库存
6. `doUpdateWallet(..., DEPOSIT)` 增加供应商钱包
7. `notificationSemisBookkeeping(..., false)` 同步供应商领料台账

这里有个关键点：不论是 `0401/0402/0403`，库存更新时传给 `updateGoodsStock(...)` 的出入库方向都是 `IN`，只是业务语义由 `bizType` 区分。

## 反审链
`infirm()` 的主顺序是：
1. 只允许 `CONFIRM`
2. 跨日反审需要权限 `GOODS_INBOUND_BILL_CROSS_DAYS`
3. 若明细已被半成品调拨引用，禁止反审
4. 单头状态改成 `INFIRM`
5. 先做库存充足校验 `checkGoodsStock(...)`
6. `giveSemisTransfer(..., true)` 回滚半成品回流量
7. `skuStockService.updateGoodsStock(..., true, IN, ...)` 冲回成品库存
8. `doUpdateWallet(..., DEBT)` 冲回供应商钱包
9. `notificationSemisBookkeeping(..., true)` 回滚供应商领料台账

## 钱包链
`doUpdateWallet(...)` 会同时生成两类钱包分录：
1. 材质钱包：
   - 材质取单头 `textureId` 对应的钱包类型
   - 金重取明细 `goldWeight` 合计
2. 现金钱包：
   - 金额取明细 `cmtFee` 合计，并四舍五入到整数

方向规则：
- 审核：`DEPOSIT`
- 反审：`DEBT`

所以成品入库单本质上也是供应商财务链入口，不只是库存链。

## 半成品/委外挂接
### 1. 半成品回流
`giveSemisTransfer(...)` 只在 `0402/0403` 生效：
1. 按 `wh_goods_inbound_semis_record` 回写 `wh_semis_transfer_detail.giveNum/giveCarat/giveGramWeight`
2. 审核时累加，反审时扣回
3. 若累计回流超过原实际用量，会直接报错
4. 当整张半成品回流单全部满足“累计 >= 实际”时，会把回流单推进到结束态

### 2. 供应商领料台账
`notificationSemisBookkeeping(...)` 会把半成品记录同步到 `SemisSupplierBreakdownApi.syncBreakdownChange(...)`：
- 审核时 `add = false`
- 反审时 `add = true`

这说明转成品入库还承担“扣供应商领料汇总”的职责。

### 3. 源单引用锁定
`referencesDetails(...)` / `referencesRecords(...)` 会把明细或半成品记录挂上引用单号：
1. 先清历史引用
2. 再校验待引用数据没有被别的单据占用
3. 最后写入 `semisTransferNo / semisBackTransferNo`

这也是反审前要检查“是否已被半成品调拨引用”的原因。

## 更新、删除、作废
### 更新
`updateGoodsInboundBill()` 只允许 `INIT/INFIRM`：
1. 重算单头快照和费用
2. 先删所有老明细
3. 再重插明细、半成品记录、附件、配件

### 删除
`deleteGoodsInboundBill()` 当前只删：
1. 主表
2. 明细表

代码里没看到同步删除半成品记录、附件、配件，也没看到状态限制。

### 作废
`discard()` 只允许 `INIT/INFIRM`，并且会额外清掉：
- `wh_outsourcing_order_detail.convertInboundNo`

这说明作废不只是改单头状态，还会解除委外明细和转成品入库单之间的挂接关系。

## 导入口径
`importFromU10` 会按 Excel `orderNo` 分组批量建单，导入时固定：
1. `bizType = 0401`
2. `billType = 04`
3. 工费金额必须和系统公式重算结果一致
4. 同一订单号下的业务日期、网点、仓库、供应商、材质必须一致

所以 U10 导入只覆盖“普通成品入库”口径，不覆盖转成品/退料回流。

## 当前结论
1. 成品入库单是“成品库存 + 供应商钱包”双链模块，不是纯库存单。
2. `0402/0403` 在同一张单里共用实现，但工费、金重校验和半成品回流链与 `0401` 不同。
3. 转成品入库审核/反审除了库存和钱包，还会同步半成品回流量、供应商领料台账和源单引用锁定。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteGoodsInboundBill` | 成品入库单 | 删除只看到了主表和明细，未见半成品记录、附件、配件的级联清理，也未见状态限制 | 待确认 | 确认删除接口是否只允许草稿态并补齐级联删除 |
| ⚠️ | `convertInbound` | 成品入库单 / 委外 | “根据委外明细转成品入库明细”方法目前直接返回空列表，整段真实实现被注释 | 待确认 | 确认该入口是否已废弃或迁移 |
| ⚠️ | `confirm/infirm` | 成品入库单 | 库存更新调用里统一传 `BizTypeEnum.G_PURCHASE_IN_STORAGE`，即使当前单据可能是 `0402/0403` | 待确认 | 确认库存日志是否因此丢失真实业务类型 |
| ⚠️ | `discard` | 成品入库单 / 委外 | 作废会直接清 `convertInboundNo` 挂接，但未见更细粒度状态约束 | 待确认 | 确认委外侧是否允许在当前状态下直接解绑 |
