# 维修单报表
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
维修单报表不是独立单据模块，而是维修域挂在 `maintainbill` 下的一组 BI 查询与导出能力。真实入口不是 `maintainbillsettle`，而是：
1. 维修主单分页/导出，输出“维修跟踪单”
2. `maintain-bill-bi` 控制器下的多维汇总报表
3. 维修明细汇总报表，串起维修单、维修出库单、维修入库单和终结信息

因此它的本质是“维修执行链的统计视图层”，不是另一条业务执行链。

## 关键入口
- 报表 Controller: [MaintainBillBiController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/MaintainBillBiController.java)
- 报表 Business: [MaintainBillBiBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/MaintainBillBiBusiness.java)
- 报表 Mapper: [MaintainBillBiMapper.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/mysql/maintainbill/MaintainBillBiMapper.java)
- 报表 SQL: [MaintainBillBiMapper.xml](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/resources/mapper/maintainbill/MaintainBillBiMapper.xml)
- 跟踪单分页/导出入口: [MaintainBillController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/MaintainBillController.java)
- 跟踪单查询实现: [MaintainBillServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintainbill/MaintainBillServiceImpl.java)

## 真实组成
### 1. 维修跟踪单
维修主单本身就带一组报表型接口：
1. `/wh/maintain-bill/page`
2. `/wh/maintain-bill/export-excel`

`exportMaintainBillExcel(...)` 导出的文件名就是“维修跟踪单”。它的数据来源是：
1. `MaintainBillMapper.selectPageData/selectListData`
2. `MaintainBillMapper.summaryData`
3. `MaintainBillServiceImpl.fillData(...)`

也就是说，维修主单页本身就是第一层报表。

### 2. 维修 BI 汇总报表
真正的“维修单报表”主入口是：
- `/wh/maintain-bill-bi`

当前已实现 6 组汇总维度：
1. `selectMaintainBillBiTotalSupplier`
2. `selectMaintainBillBiTotalCustomer`
3. `selectMaintainBillBiTotalDept`
4. `selectMaintainBillBiTotalStaff`
5. `selectMaintainBillBiTotalWare`
6. `selectMaintainBillBiTotalWareName`

这些报表都不是直接查维修单头，而是把：
1. `wh_maintain_out_bill + detail`
2. `wh_maintain_in_bill + detail`

两条执行链分别聚合后，再按统一维度做 `UNION + LEFT JOIN` 对照，输出“送厂”和“回厂”两侧指标。

### 3. 维修汇总明细报表
最核心的报表接口是：
1. `/selectMaintainBillBiTotalDetail`
2. `/exportMaintainBillBiTotalDetail`

它查询的不是单表，而是把：
1. `wh_maintain_bill`
2. `wh_maintain_bill_detail`
3. `wh_maintain_out_bill/_detail`
4. `wh_maintain_in_bill/_detail`
5. 客户、供应商

整条维修执行链拼成一张“维修明细生命周期报表”。

## 汇总口径
### 1. 供应商维度
供应商汇总以维修出库单和维修入库单为基础，按以下维度聚合：
1. `shop_id`
2. `biz_date`
3. `supplier_id`
4. `big_class_id`
5. `category_id`

输出两侧指标：
1. `out_num/out_gold_weight/out_gross_weight/out_cmt_fee`
2. `in_num/in_gold_weight/in_gross_weight/in_cmt_fee`

只统计：
1. `bill_status = '1'`
2. `deleted = 0`

### 2. 客户维度
客户汇总不是直接按维修单头全量统计，而是强制收敛到客户侧业务类型。控制器里先写死：
1. `MAINTAIN_SKU_BILL_CUSTOMER`
2. `MAINTAIN_SKU_BILL_EXHIBITION`

再按：
1. `shop_id`
2. `customer_id`
3. `business_staff_id`
4. `biz_date`
5. `big_class_id`
6. `category_id`

聚合送厂与回厂两侧指标。

### 3. 部门、业务员、仓库、仓名维度
这些都是同一类思路：
1. 先分别汇总维修出库链和维修入库链
2. 再按对应维度并表输出

差别只在分组键：
1. 部门：`dept_id`
2. 业务员：`business_area_id + business_staff_id`
3. 仓库：`ware_id + local_id + dept_id`
4. 仓名：`ware_id`

### 4. 明细报表
明细报表的基础主键是 `wh_maintain_bill_detail.id`。它先选出每条维修明细，再去关联：
1. 最新或历史维修出库单
2. 对应维修入库单
3. 终结字段

主表行输出：
1. 维修单号、明细号、条码、件数、金重
2. 客户、业务分区、业务员、材质、仓库
3. 维修出库单号/供应商/金重/工费
4. 维修入库单号/供应商/金重/工费
5. `replyStatus`
6. `finalizationReason/finalizationTime/finalizationMan`

其中 `replyStatus` 在报表层不是直接照抄数据库，而是业务重算：
1. 默认先写“未回”
2. 若存在一笔有效维修入库，则写“已回”
3. 若 `finalizationReason` 非空，则直接覆盖成“终结”

## 明细筛选口径
`MaintainBillBiTotalDetailReqVO` 支持的关键筛选包括：
1. 时间区间
2. 材质、仓库
3. 客户、供应商
4. 业务分区、业务员
5. `bizType`
6. 维修单号、维修明细号、条码
7. 维修出库单号/回厂单号
8. 件数、金重、送厂工费、回厂工费区间
9. `replyType`

其中 `replyType` 是报表侧的特别口径：
1. `1`：已回，条件是 `out_num > 0 AND out_num <= in_num`
2. `2`：未回，条件是 `out_num > in_num AND reply_status <> '4'`
3. `3`：在厂，条件是 `out_num = 0 AND reply_status <> '4' AND step_ware_id = 457`

这说明报表的“已回/未回/在厂”并不完全等于明细表原始 `replyStatus`，而是额外叠了累计量和仓位条件。

## 小计与汇总
`MaintainBillBiBusiness` 里，供应商、客户、业务员、仓名报表都支持 `isSubtotal`：
1. 指定分组字段
2. 自动插入“小计”行
3. 小计行重新汇总 `out_*` 和 `in_*`

明细报表则额外拆两层汇总：
1. `selectMaintainBillBiTotalDetailBillSummary`：先按维修明细去重，汇总基础 `num/goldWeight`
2. `selectMaintainBillBiTotalDetailSummary`：汇总维修出库和维修入库两侧 `goldWeight/cmtFee`

所以它输出的是“双层汇总”：
1. 维修主明细总数/总金重
2. 送厂/回厂两侧累计金重和工费

## 与维修结算明细的关系
`maintainbillsettle` 并不是“维修单报表”模块。它更像维修结算明细表的 CRUD 和导出：
1. 按 `billNo/settleNo/materialNo/settleClass` 查
2. 不联维修入出库执行链
3. 不做送厂/回厂对照聚合

因此总览里的“维修单报表”应归到 `maintain-bill-bi`，不应误认成 `maintainbillsettle`。

## 当前结论
1. 维修单报表真实入口是 [MaintainBillBiController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/maintainbill/MaintainBillBiController.java)，不是独立 `report` 模块，也不是 `maintainbillsettle`。
2. 报表层的核心思路是把维修出库链和维修入库链按统一维度做“送厂/回厂”双侧对照。
3. 维修汇总明细报表是维修域最完整的查询视图，直接把维修单、维修出库单、维修入库单和终结信息串成一张生命周期表。
4. 报表层存在自己的状态口径，尤其 `replyType` 和最终展示的“未回/已回/终结”，不能和明细表原始 `replyStatus` 直接等同。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `selectMaintainBillBiTotalCustomer` | 维修单报表 | 控制器里把客户报表业务类型直接写死成 `客户维修 + 展厅维修`，若后续新增客户侧维修类型，报表会天然漏数 | 待确认 | 确认这两个 `bizType` 是否就是客户报表的完整白名单 |
| ⚠️ | `selectMaintainBillBiTotalDetail_from_where` | 维修单报表 | `replyType=3` 用 `step_ware_id = 457` 判断“在厂”，属于硬编码仓库 ID 口径 | 待确认 | 确认 `457` 是否稳定代表“厂修在途/在厂仓” |
| ⚠️ | `selectMaintainBillBiTotalDetailFillDataExpand` | 维修单报表 | 报表展示的“已回/未回/终结”是业务重算结果，不完全等于明细表 `replyStatus` 原值 | 待确认 | 联动前端确认页面文案是否明确“这是报表状态，不是单表原始状态” |
| ⚠️ | `maintainbillsettle` | 维修单报表 / 维修结算明细 | 结算明细模块名字容易被误判成“维修报表”，但它实际上只是 `wh_maintain_bill_settle` 的 CRUD/导出 | 待确认 | 总览和后续分析时明确把 `maintain-bill-bi` 视为维修报表主入口 |
