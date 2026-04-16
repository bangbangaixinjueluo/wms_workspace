# 成品退厂登记单
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
成品退厂登记单对应 `BillTypeEnum.GOODS_REJECT_RECORD(05)`，实际覆盖三种业务：
1. `0501 = G_REJECT_RECORD_EXHIBITION`：普通退厂登记
2. `0502 = G_REJECT_RECORD_QC`：QC 退厂登记
3. `0503 = G_REJECT_RECORD_MAINTAIN`：维修退厂登记

它本身不是最终出库执行单，更像“退厂候选池/可退厂池”：
1. 先把可退厂成品登记出来
2. 审核后才允许被成品退厂单引用
3. 被引用后通过 `outOfWare` 表示是否已出库

因此它的主职责是“登记与占用控制”，不是库存或钱包过账。

## 关键入口
- Controller: [GoodsRejectRecordController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/goodsrejectrecord/GoodsRejectRecordController.java)
- Service: [GoodsRejectRecordServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsrejectrecord/GoodsRejectRecordServiceImpl.java)
- 分厂业务: [GoodsRejectRecordAllocateFacBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/GoodsRejectRecordAllocateFacBusiness.java)

## 涉及数据表
| 表名 | 角色 | 说明 |
|------|------|------|
| `wh_goods_reject_record` | 主表 | 退厂登记单头 |
| `wh_goods_reject_record_detail` | 明细表 | 单件或 SKU 级退厂登记明细 |
| `wh_goods_reject_bill` | 执行单 | 审核后的登记单可被退厂单引用，引用后回写 `rejectBillId` |
| `wh_maintain_bill_detail` | 维修明细 | `0503` 会占用维修明细并累计 `recordNum` |

## 建单逻辑
### 1. 明细类型识别
`recordValid()` 会先把明细分成三类：
1. 只有单件
2. 只有成品
3. 单件和成品混合

结果写入单头 `detailItemType`。

### 2. 维修登记约束
`0503 = G_REJECT_RECORD_MAINTAIN` 有额外限制：
1. 不能混入非维修明细
2. 同一个维修明细不能重复引用
3. 建单后会把 `wh_maintain_bill_detail.recordId` 标到当前登记单

### 3. 头汇总后端重算
`convertDetails()` 会重算 `totalNum/totalGoldWeight/totalWeight`，所以登记单头合计值也是后端口径。

### 4. 分厂能力
`GoodsRejectRecordAllocateFacBusiness` 支持按明细 `supplierCode` 自动拆单：
1. `allocate()`：按供应商分组后批量创建多张登记单
2. `allocateAndCheck()`：按供应商分组创建后立即逐张审核

## 审核链
`confirm()` 的主顺序是：
1. 校验仓库不为空
2. 只拦重复审核，没有显式限定只能 `INIT/INFIRM`
3. 校验明细里的供应商编码要和单头供应商一致
4. 单头改成 `CONFIRM`，写入 `approvedMan/approvedTime`
5. `maintainBillHandle(..., true)`，仅维修明细生效，累加 `wh_maintain_bill_detail.recordNum`

这里没有库存、钱包、单件状态、成品库存写回动作，说明登记单审核只是把数据放进“可执行退厂池”。

## 反审链
`infirm()` 的主顺序是：
1. 只允许 `CONFIRM`
2. `outOfWare` 不能是 `OUTED`
3. 单头改成 `INFIRM`
4. `maintainBillHandle(..., false)` 回滚维修明细累计 `recordNum`

## 作废链
`discard()` 的主顺序是：
1. 不能是 `CONFIRM`
2. 不能重复作废
3. `outOfWare` 不能是 `OUTED`
4. 单头改成 `DISCARD`
5. 若是 `0503`，释放 `wh_maintain_bill_detail.recordId`

## 删除
`delete()` 目前是直接物理删除主表和明细，代码里没看到状态限制，也没看到对维修占用的释放逻辑。

## 出库占用语义
登记单还有一个关键字段：`outOfWare`
1. `NOT_OUT(1)`：未出库
2. `OUTING(2)`：出库中
3. `OUTED(3)`：已出库

它不是自身审核状态，而是被成品退厂单引用后的执行进度：
1. 退厂单建单绑定时改成 `OUTING`
2. 退厂单审核时改成 `OUTED`
3. 退厂单反审时又改回 `OUTING`
4. 退厂单作废/解绑时才回到 `NOT_OUT`

## 当前结论
1. 成品退厂登记单是“退厂候选池”，不是实际库存出库单。
2. 它真正承接的核心动作是：供应商分池、维修明细占用、被退厂单引用。
3. `outOfWare` 才是它和成品退厂单之间的桥接字段，单看 `billStatus` 不够。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `delete(Long id)` | 成品退厂登记单 | 删除直接物理删主表和明细，未见状态限制，也未见维修占用释放 | 待确认 | 确认删除接口是否只允许草稿态，且是否应补 `recordId` 释放 |
| ⚠️ | `confirm(Long id)` | 成品退厂登记单 | 审核只拦“重复审核”，未显式限制 `DISCARD` 等异常状态再次审核 | 待确认 | 确认是否应统一改成 `BillStatusEnum.confirmCheck(...)` |
| ⚠️ | `infirm(Long id)` | 成品退厂登记单 / 成品退厂单 | 反审只禁止 `OUTED`，说明仍被退厂单占用的登记池不能直接退出 | 待确认 | 确认 `OUTING` 语义是否就是“仍被退厂单占用” |
