# 维修结算明细
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修结算明细不是维修报表，也不是独立审核执行单，而是维修单的结算子表 `wh_maintain_bill_settle`。它的核心职责只有两层：
1. 为维修单保存“材质/结价/金额”结算行。
2. 在维修确认链里作为客户钱包与结价冲回的钱包来源数据。

所以它更像“维修单财务口径明细池”，不是单独业务状态机。

## 关键入口
- Controller: [MaintainBillSettleController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbillsettle/MaintainBillSettleController.java)
- Service: [MaintainBillSettleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainbillsettle/MaintainBillSettleServiceImpl.java)
- DO: [MaintainBillSettleDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/maintainbillsettle/MaintainBillSettleDO.java)
- 维修确认链引用: [MaintainBillConfirmBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainBillConfirmBusiness.java)
- 维修建单 VO: [MaintainBillStoreVO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/vo/MaintainBillStoreVO.java)
- 维修结算 VO: [MaintainBillStoreSettleVO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/vo/MaintainBillStoreSettleVO.java)

## 数据结构
表：`wh_maintain_bill_settle`

关键字段：
1. `billId/billNo`：所属维修单。
2. `settleNo`：结算编码。
3. `originWeight`：来料金重。
4. `convertRate/convertWeight`：折算系数与折算重。
5. `purifyUnitFee/purifyUnitFeeTaxed`：提纯单价。
6. `settleWeight`：结算重量。
7. `settleMoney`：结算金额。
8. `goldPrice/countPrice/countMoney`：计价口径。
9. `materialNo`：钱包材质维度。
10. `settleClass`：结算类别。

从字段设计看，这张表承载的是“维修单财务结算行”，不是过程状态。

## 模块真实行为
### 1. 对外接口能力
`MaintainBillSettleController` 暴露的只有：
1. `create`
2. `update`
3. `delete`
4. `get`
5. `list`
6. `page`
7. `export-excel`

对应 `MaintainBillSettleServiceImpl` 也基本都是标准 CRUD：
1. `createMaintainBillSettle(...)`
2. `updateMaintainBillSettle(...)`
3. `deleteMaintainBillSettle(...)`
4. `findByBillId(...)`
5. `removeByBillId(...)`
6. `removeByData(...)`
7. `insertBatch(...)`
8. `updateBatch(...)`

当前没有自己的审核、反审、作废、确认状态流。

### 2. 在维修确认链里的真实作用
`MaintainBillConfirmBusiness.ConfirmData` 初始化时会同时加载：
1. 维修单头
2. 维修明细
3. 维修结算明细 `settleDOList = maintainBillSettleService.findByBillId(id)`

然后这张表会直接参与两组钱包数据构造：

#### 普通钱包数据 `createWalletBaseDTO(...)`
1. 过滤 `materialNo` 非空的结算行。
2. 按 `materialNo + settleClass` 聚合 `settleWeight`。
3. 生成材质钱包数据。
4. 再额外拼一条现金钱包：
   - `materialNo = CASH.defaultMaterialNo`
   - `settleClass = SETTLE_CASH`
   - `balance = totalSettleMoney`

也就是说，维修确认链会把维修结算明细同时拆成：
1. 材质钱包重量
2. 现金钱包金额

#### 结价冲回钱包 `createCloseWalletBaseDTO(...)`
1. 只取 `settleNo == CLOSING_PRICE`
2. 且 `materialNo` 非空
3. 同样按 `materialNo + settleClass` 聚合 `settleWeight`

这说明维修链里的“结价冲回”是从维修结算明细中过滤出来的专门分支，不是另算。

## 与维修单主模块的关系
### 1. VO 层已声明结算列表
`MaintainBillStoreVO` 明确带有：
1. `detailList`
2. `settleList`

说明维修单表单模型本来就预留了结算明细输入。

### 2. 但主服务当前没看到明显落表
当前可见的 `MaintainBillServiceImpl`：
1. `createMaintainBill(...)` 只看到 `createBill(...) + storeDetail(...) + summaryTotal(...)`
2. `updateMaintainBill(...)` 只看到改单头、重存明细、重算合计
3. `getMaintainBillVO(...)` 只回填了单头和 `detailList`

没有在这些主流程里看到：
1. `maintainBillSettleService.insertBatch(...)`
2. `maintainBillSettleService.removeByBillId(...)`
3. `vo.setSettleList(...)`

因此当前代码呈现出一个很强的信号：维修结算明细表虽然被维修确认链消费，但在维修单主服务的建单/改单/详情回显里，没有看到对它的完整接线。

## 状态口径
维修结算明细模块自身没有审核态。它只有：
1. 行级 CRUD
2. 按 `billId` 归属到维修单
3. 在维修确认时被当作结算输入明细读取

因此它的生命周期实际依附于维修单，而不是自身独立流转。

## 风险与待确认
1. `MaintainBillStoreVO` 已带 `settleList`，但 `MaintainBillServiceImpl.create/update/getMaintainBillVO` 当前没看到对应的保存、删除、回显逻辑，需确认这条链是否漏接。
2. `MaintainBillConfirmBusiness` 会直接按 `findByBillId(...)` 读取结算明细参与钱包计算；如果维修单主流程没正确落这张表，客户钱包与结价冲回就会失真。
3. `MaintainBillSettleController.deleteMaintainBillSettle(...)` 只是物理删除单行，未见状态限制，也未校验所属维修单是否已审核。
4. `MaintainBillSettleMapper` 只有简单条件查询，未做结算编码合法性、重复性、业务类型映射校验；当前更像裸表操作。
5. `getMaintainBillVO(...)` 没有 `settleList` 回显，前端若依赖维修单详情回显结算行，可能拿不到这部分数据。

## 结论
维修结算明细应被定位为“维修单财务结算子表”。它本身不驱动状态，但会被维修确认链直接拿来构造客户钱包和结价冲回钱包，因此属于维修域里很关键的财务输入层。后续如果要改维修确认、客户钱包、结价冲回，必须把 `wh_maintain_bill_settle` 一起看；如果要补维修单建单/详情能力，这张表大概率还有未接完整的主流程。
