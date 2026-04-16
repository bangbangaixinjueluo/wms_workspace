# 成品转料单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
成品转料单对应 `BillTypeEnum.GOODS_RECYCLE`，业务类型固定为 `3201 = G_RECYCLE`。

它不是普通“成品扣库单”或“原料入库单”，而是三条链一起落账：
1. 成品库存扣减
2. 原料库存增加
3. 单件转料状态流

如果明细里带 `itemNo`，还会下钻到单件转料服务 `RecycleBillService.doSubmit(...)`。

## 关键入口
- Controller: [GoodsRecycleBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/goodsrecyclebill/GoodsRecycleBillController.java)
- Service: [GoodsRecycleBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsrecyclebill/GoodsRecycleBillServiceImpl.java)
- 单件转料服务: [RecycleBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/recyclebill/RecycleBillServiceImpl.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_goods_recycle_bill` | 主表 | 成品转料单头 |
| `wh_goods_recycle_detail` | 明细表 | SKU 或单件转料明细 |
| `wh_material_stock` | 原料库存 | 审核入库、反审回滚 |
| `wh_material_stock_log` | 原料库存日志 | 记录转料入库过账痕迹 |
| `wh_sku_stock` | 成品库存 | 审核扣减、反审回补 |
| `wh_recycle_bill` | 单件转料单 | 当明细带 `itemNo` 时，由成品转料单驱动其状态流 |
| `wh_recycle_detail` | 单件转料明细 | 单件转料的 EPC 明细 |
| `wh_item` | 单件表 | 单件转料审核时从 `ON_WARE` 改到 `CONVERTED` |
| `wh_item_log` | 单件日志 | 记录单件转料状态变化 |

## 建单逻辑
### 1. 主表约束
建单和更新都要求单头 `shopId/wareId` 不为空。

### 2. 明细重算
`convertDetails()` 会重算：
1. `totalNum`
2. `totalGoldWeight`

同时约束每行：
1. `num` 和 `goldWeight` 不能同时为 0
2. `weight` 为空时默认等于 `goldWeight`
3. `grossWeight` 为空时默认等于 `weight`

所以单头件数和金重都是后端汇总口径。

### 3. 明细分流
审核和反审都会把明细拆成两组：
1. `itemNo` 为空：成品明细
2. `itemNo` 非空：单件明细

这也是这个模块最核心的分支点。

## 审核链
`submit()` 的主顺序是：
1. `BillStatusEnum.confirmCheck(...)`
2. 单头改成 `CONFIRM`，写入 `approvedMan/approvedTime`
3. 查询全部转料明细
4. 把明细拆成成品明细和单件明细

### 成品明细分支
1. `doMaterialStock(..., POSITIVE)`
   - 按单头 `materialNo` 聚合入原料库存
   - 原料库存写入目标仓 `destWareId`
2. 组装 `GoodsSkuStockBO`
3. `skuStockService.checkGoodsStock(...)`
4. `skuStockService.updateGoodsStock(..., false, OUT, BizTypeEnum.getEnum(billDO.getBizType()))`
   - 扣减成品库存

### 单件明细分支
1. 校验单件存在
2. 校验单件必须同时满足：
   - `itemStatus = ON_WARE`
   - `lockStatus = UN_LOCK`
3. 构造 `RecycleBillDO`
   - `billType = SINGLE_TRANSFER_SINGLE`
   - `bizType = SG_TRANSFER_SINGLE`
4. 调 `recycleBillService.doSubmit(...)`
   - 把单件转料状态流一并推进到 `CONFIRM`
   - 单件状态改成 `CONVERTED`

## 反审链
`unConfirm()` 的主顺序是：
1. `BillStatusEnum.infirmCheck(...)`
2. 跨日反审要权限 `GOODS_RECYCLE_BILL_CROSS_DAYS`
3. 单头改成 `INFIRM`
4. 再次拆成成品明细和单件明细

### 成品明细分支
1. `doMaterialStock(..., NEGATIVE)`
   - 回滚原料库存增加
2. `skuStockService.updateGoodsStock(..., true, OUT, BizTypeEnum.getEnum(...))`
   - 回滚成品库存扣减

### 单件明细分支
1. 重新构造单件转料数据
2. 调 `recycleBillService.doSubmit(...)`
   - 这次传的是反审口径
   - 单件状态从 `CONVERTED` 回到 `ON_WARE`

## 原料库存链
`doMaterialStock()` 的口径很直接：
1. 只认单头 `materialNo`
2. 把明细 `goldWeight/grossWeight` 汇总到这一种原料上
3. 入库/回滚都写到 `destWareId`

因此成品转料不是“明细各自带原料编码”的模型，而是整张单头指定一个目标原料编码。

## 单件转料附加链
`RecycleBillServiceImpl` 里可以看到，单件转料单自身也是一条完整链：
1. 审核时：
   - 原料库存增加
   - 单件状态从 `DISCARD` 改到 `CONVERTED`
   - 扣减成品库存
2. 反审时：
   - 原料库存回滚
   - 单件状态从 `CONVERTED` 改回 `ON_WARE`
   - 回补成品库存

而成品转料单调用 `doSubmit(...)` 时，是把自己的单件明细直接托管给这条链执行。

## 删除、作废、更新
### 更新
`updateGoodsRecycleBill()` 只允许 `INIT/INFIRM`，会删旧明细再重插。

### 删除
`deleteGoodsRecycleBill()` 直接删主表和明细，未见状态限制。

### 作废
`discard()` 走 `BillStatusEnum.obsoleteCheck(...)`，只改单头状态为 `DISCARD`，没有库存回滚逻辑。

## 当前结论
1. 成品转料单不是钱包链，而是“成品库存出库 + 原料库存入库 + 单件转料状态流”三链组合。
2. 是否带 `itemNo` 决定它是只走“成品转料主链”，还是继续下钻到“单件转料子链”。
3. 原料库存口径完全吃单头 `materialNo + destWareId`，不是明细逐行指定目标原料。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `deleteGoodsRecycleBill` | 成品转料单 | 删除直接物理删主表和明细，未见状态限制 | 待确认 | 确认删除接口是否仅允许草稿态 |
| ⚠️ | `discard` | 成品转料单 | 作废只改单头状态，不回滚库存；必须依赖状态机保证未审核单据才可作废 | 待确认 | 确认 `BillStatusEnum.obsoleteCheck()` 的实际可作废状态 |
| ⚠️ | `unConfirm` | 成品转料单 | 成品分支回滚原料库存时当前传的是 `details` 而不是 `goodsDetails`，需要确认单件明细是否会被一并计入原料回滚 | 待确认 | 复核反审时原料库存汇总口径是否和审核完全对称 |
