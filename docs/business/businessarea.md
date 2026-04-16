# 业务分区

## 1. 模块定位

- 对应表：`wh_business_area`、`wh_business_area_dept`
- 入口：`BusinessAreaController`、`BusinessAreaServiceImpl`
- 角色：客户、业务员、订单和权限过滤链的组织区域宿主

## 2. 核心职责

- 维护业务分区编号、名称、经理用户
- 提供分区与部门的映射
- 支持按用户、按名称、按 ID 回查业务分区
- 为业务员和客户域补 `areaName/managerName/managerPhone`

## 3. 代码结论

- `createBusinessArea(...)` 会校验 `areaNo` 不重复
- `deleteBusinessArea(...)` 不只删分区，还会逐条删 `businessAreaDept`
- `getNextAreaNo()` 当前是 3 位自增字符串
- `fillData(...)` 会把部门名列表和经理昵称补到响应对象

## 4. 关注点

- `⚠️` create/update 里给 `businessAreaDept` 建联的代码目前整体被注释，真实维护入口需要确认
- `⚠️ deleteBusinessArea(...)` 只看到删 area-dept，未见对业务员、客户、订单历史引用保护
- `⚠️ getNextAreaNo()` 固定 3 位，容量边界需要确认
