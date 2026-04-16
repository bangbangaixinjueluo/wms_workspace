# 字印单明细

## 模块定位
- 对应表：`wh_marking_detail`
- 入口：`MarkingDetailController`、`MarkingDetailServiceImpl`
- 这不是独立执行单，而是字印单的明细宿主；字印处理到哪一件、哪条 EPC、哪条已完成，都落在这里。

## 主数据职责
- 关键字段包括：
  - `billNo`
  - `itemNo/epc`
  - `skuId/skuNo/categoryId`
  - `bigClassId/textureId`
  - `printStatus`
  - `weight/goldWeight/grossWeight/ropeWeight/tareWeight`
  - `specs/modelNo`
  - `num`
  - `effective`
- `printStatus` 是字印明细执行态；`effective` 则承担失效标记，不是直接物理删历史。

## 主要业务动作
- `createMarkingDetail(...)`
  - 新增单条字印明细。
- `updateMarkingDetail(...)`
  - 修改单条字印明细。
- `deleteMarkingDetail(...)`
  - 当前是物理删除。
- `updateMarkingDetailByOrderNo(billNo, detailDO)`
  - 按单号批量更新。
  - 当前主要改两类字段：
    - `printStatus`
    - `effective`
- `getPage(...)`
  - 这不是简单单表分页。
  - 会联查：
    - `wh_marking_bill`
    - `wh_sku`
    - `wh_customer`
    - `business_staff`
    - `wh_customer_marking`
    - `wh_warehouse`
  - 最终形成“字印明细执行视图”。

## 与字印单主链的关系
- `markingbill.confirm(...)`
  - 审核前会校验明细非空。
- `markingbill.marked(...)`
  - 会整单把当前 `billNo` 下全部明细 `printStatus` 改成已字印。
- `markingbill.obsolete(...)`
  - 会整单把当前 `billNo` 下全部明细 `effective` 改成失效。

## 展示口径
- `getMarkingDetailVO/getMarkingDetailVOList`
  - 会补 SKU 图片和名称。
- `getPage(...)`
  - 会继续补客户名、业务员、仓库名、字印内容、单头状态、完工时间和操作人昵称。
- 所以前端看到的字印明细页，本质上是“字印单 + 明细 + SKU + 客户 + 字印内容”的聚合页。

## 关键结论
- 字印单明细不是普通子表，而是字印执行域里真正可追踪到 EPC/条码层的执行明细池。
- 要判断某张字印单处理到哪一件，不能只看单头，必须落回 `wh_marking_detail.printStatus`。

## 风险与疑点
- `⚠️` `deleteMarkingDetailByBillNo(...)` 目前是空实现；如果有调用方以为它会真删，会出现预期落空。
- `⚠️` `getExcleList(...)` 当前直接返回空列表，导出能力看起来未完成。
- `⚠️` 明细分页里 `printStatus` 过滤当前写的是比对单头 `billStatus`，不是比对明细 `printStatus`，需要确认这是不是有意设计。
