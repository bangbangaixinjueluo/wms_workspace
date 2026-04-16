# 收款分账明细

## 模块定位
- 对应表：`wh_receivable_divide_bill`
- 入口：`ReceivableDivideBillController`、`ReceivableDivideBillServiceImpl`
- 这不是独立审核执行单，而是收款单分账结果的子表宿主，用来承接分账后的拆分结果。

## 主数据职责
- 关键识别口径是：
  - `billNo`
  - 分账对象、金额、金重等拆分字段
- 服务层主要是 CRUD 与按收款单号查询 `getDivideInfo(billNo)`。

## 主要业务动作
- `create/update/delete`
  - 都是标准子表 CRUD。
- `getReceivableDivideBillByBillNo(billNo)`
  - 按分账单号查询单条。
- `getDivideInfo(billNo)`
  - 按单号取整组分账结果。
  - 若单号为空直接返回空集合。
  - 若查不到会直接抛“单号不存在”。

## 在主链中的作用
- `receivablebill`
  - 收款单做分账/取消分账时，真正拆分后的归属结果要落回这张表。
- 这张表本身不处理银行卡入账，只处理“钱包归属拆分结果”这层事实。

## 关键结论
- 收款分账明细不是独立业务单，而是收款单分账侧链的结果表。
- 分账是否完成、拆给了谁、拆了多少，最终要回到这张表看。

## 风险与疑点
- `⚠️` `deleteReceivableDivideBill(...)` 当前是物理删除，未见状态限制。
- `⚠️` `fillData(...)` 当前为空实现，说明展示补数口径很薄，若后续前端要求更多展示字段，可能要重新梳理来源。
- `⚠️` `getDivideInfo(...)` 查不到直接按“单号不存在”抛错，调用方如果把“尚未分账”也走这条，会和“异常丢单”混在一起。
