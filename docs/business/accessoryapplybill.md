# 辅料领用单

## 1. 模块定位

- 对应表：`wh_accessory_apply_bill`
- 入口：`AccessoryApplyBillController`、`AccessoryApplyBillServiceImpl`
- 角色：配件域里的领用申请单头，承接单号、业务类型、流程结果与基础组织信息

## 2. 核心职责

- 记录领用申请单头
- 保存 `processResult/processUserId/processInstanceId` 这组流程字段
- 提供按 `billNo/shopNo/wareNo/supplierNo` 的基础查询

## 3. 代码结论

- 当前 service 只有标准 CRUD，没有像入库/出库那样的 `confirm/infirm/discard`
- `create/update` 会补 `creatorName/updaterName`
- 单头自身带流程状态 `processResult`，但当前控制器侧没有看到真正驱动 BPM 回写的业务层封装

## 4. 关注点

- `⚠️ deleteAccessoryApplyBill(...)` 是物理删除，没看到流程中、已通过等状态限制
- `⚠️` 目前只看到单头 service，未见单独业务宿主统一处理领用审批、库存占用或出库承接
- `⚠️` `status` 与 `processResult` 双状态并存，真实以哪层为准需要继续确认
