# 付款单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`payablebilldetail` 是付款单的事实明细层，与收款单明细基本镜像。真正分笔付了哪些卡、多少金额，最终都落在这张表。

## 关键入口
- Service: [PayableBillDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/payablebilldetail/PayableBillDetailServiceImpl.java)

## 真实行为
- `insertBatch(details)` / `removeByBillId(id)`
  - 付款单重建时整批删改明细。
- `getPayableBillDetailPage(...)`
  - 分页走 mapper `selectPageData(...)`。
- `getPayableBillDetailList(...)`
  - 导出时先取明细，再额外追加一条汇总记录。
- `getPayableBillDetailByBillNo/getPayableBillDetailByBankCode`
  - 承接单号和银行卡维度查询。

## 在主链中的作用
- `payablebill`
  - 主单审核/反审负责“银行卡扣减 + 钱包增加”；明细层承接分笔付款事实。

## 风险与待确认
1. `⚠️` `deletePayableBillDetail(...)` 和 `removeByBillId(...)` 当前都是物理删除，未见状态限制。
2. `⚠️` 导出末尾同样手工追加汇总行，消费侧要明确区分。
3. `⚠️` `getPayableBillDetailByBankCode(...)` 也是假设一张卡只命中一条明细，需确认业务是否允许重复。

