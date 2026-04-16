# 客户业务员变更单明细

## 模块定位
- 入口：`CustomerStaffUpdateDetailServiceImpl`
- 宿主表：`wh_customer_staff_update_detail`
- 这不是薄子表，而是客户归属调整的“明细事实层 + 报表补数层”。

## 关键入口
- `getCustomerStaffUpdateDetailPage(...)`
- `getRespByBillId(...)`
- `getCustomerStaffUpdateDetailByCustomerNo(...)`

## 真实行为
- 分页查询直接走 mapper 的 `selectJoinPage(...)`，不是简单单表分页。
- `fillList(...)` 会批量补齐：
  - 客户名称
  - 原分区经理名
  - 调整后分区经理名和手机号
  - 调整后业务员名和手机号
  - 单头 `bizAdjustDate`
- 这些补数依赖 `customer`、`businessarea`、`businessstaff`、`adminUser` 和单头表联合装配。

## 在主链中的作用
- 承接客户归属调整的逐行事实。
- 给详情页、分页页、导出页提供“变更前后是谁”的可读视图。

## 关键结论
- 这层的价值不只是存明细，而是把“前后归属差异”组装成可审可查的页面视图。

## 风险与待确认
- `⚠️` `deleteCustomerStaffUpdateDetail(...)` 仍是物理删除。
- `⚠️` 明细页大量依赖运行时补数，若组织主数据被改名或删除，历史展示会跟着漂移。
