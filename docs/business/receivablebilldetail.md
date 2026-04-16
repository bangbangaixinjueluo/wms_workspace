# 收款单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`receivablebilldetail` 是收款单的事实明细层，不是独立审核单。银行账户、收款方式、分笔金额这些“收了什么钱”的行级事实，最终都在这里落。

## 关键入口
- Service: [ReceivableBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/receivablebilldetail/ReceivableBillDetailServiceImpl.java)

## 真实行为
- `insertBatch(details)`
  - 主单建单/改单时整批写入。
- `removeByBillId(id)`
  - 主单重建时按 billId 整批物理删除。
- `getReceivableBillDetailPage(...)`
  - 分页走自定义 mapper `getPage(...)`。
- `getReceivableBillDetailList(...)`
  - 导出时会在明细列表末尾手工追加一条 summary。
- `getReceivableBillDetailByBillNo/getReceivableBillDetailByBankCode`
  - 分别承接按业务单号和按银行卡编码查询。

## 在主链中的作用
- `receivablebill`
  - 主单审核时处理“银行卡 + 钱包”资金链；明细层承接分笔收款事实。

## 风险与待确认
1. `⚠️` `deleteReceivableBillDetail(...)` 和 `removeByBillId(...)` 当前都是物理删除，未见主单状态保护。
2. `⚠️` 导出列表会手工追加 summary 行，前端/导出侧需要区分普通明细与汇总行。
3. `⚠️` `getReceivableBillDetailByBankCode(...)` 默认按 `bankCode` 取单条，若同卡号存在多行明细需确认是否足够。

