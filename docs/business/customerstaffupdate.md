# 客户业务员变更单

## 模块定位
- 入口：`CustomerStaffUpdateController`、`CustomerStaffUpdateServiceImpl`
- 宿主表：`wh_customer_staff_update`、`wh_customer_staff_update_detail`
- 这不是普通改单，而是“客户归属迁移申请 + BPM 审批入口 + 跨模块归属回写宿主”。

## 关键入口
- `/wh/customer-staff-update/create`
- `/wh/customer-staff-update/update`
- `/wh/customer-staff-update/confirm`
- `/wh/customer-staff-update/reconfirm`
- `/wh/customer-staff-update/discard`
- `/wh/customer-staff-update/importDetail`

## 真实行为
- 建单时生成 `CUTOMER_STAFF_UPDATE` 前缀单号，并批量保存明细。
- 明细保存会强校验客户、原业务分区/经理/业务员、变更后分区/经理/业务员都不能为空。
- 改单只允许 `INIT/INFIRM`，且会先删旧明细再整批重建。
- 提交审核时会：
  - 校验明细不为空
  - 校验单据未在 BPM 审批中
  - 校验客户、业务分区、业务员等引用主数据存在
  - 调用 `BpmProcessInstanceApi` 发起流程，并把单头切到 `CONFIRMING`
- 该 service 直接依赖应收应付、来料出料、销售退货、调拨、客户订单、客留、维修、展销会等大量 mapper，说明它的真实目标是做“客户归属整体迁移”。

## 在主链中的作用
- 统一承接客户从原业务员/分区/经理迁到新归属人的申请。
- 作为跨模块回写入口，把历史业务单和后续业务归属一起迁过去。
- 作为 BPM 审批链的宿主，控制何时真正生效。

## 关键结论
- 这是客户域里最重的“组织归属重写宿主”之一，不是简单资料改单。
- 真正风险不在单头状态，而在它跨越了大量业务表。

## 风险与待确认
- `⚠️` `deleteCustomerStaffUpdate(...)` 仍是物理删主表+明细，未见对已发起流程或已生效回写的保护。
- `⚠️` 改单链采用“删旧明细后重建”，若后续明细挂了外部引用会有断链风险。
- `⚠️` 该模块跨应收、订单、来料、销售、客留、维修等大量业务表，后续若新增客户归属字段，很容易漏接这里。
