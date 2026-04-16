# 抄送配置
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`copysetting` 不是普通后台参数表，而是新建款、改款审批域里的“抄送可见规则 + 转办候选规则”宿主。它按 `menuId` 维护：
- 抄送人 `ccRecipientIds`
- 转办人 `transferIds`

并被 `newsku / updatesku` 企微待办页、已审/已驳回页、抄送人列表和转办人列表直接消费。

## 关键入口
- Controller: [CopySettingController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/copysetting/CopySettingController.java)
- Service: [CopySettingServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/copysetting/impl/CopySettingServiceImpl.java)
- Entity: [CopySetting.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/copysetting/CopySetting.java)

## 真实行为
### 1. 这层是按菜单维度配置审批协同规则
`copy_setting` 的关键字段包括：
- `settingName`
- `menuId`
- `ccRecipientIds`
- `transferIds`
- `status`

说明它不是按业务单据或审批流实例配规则，而是按“菜单/功能入口”配置抄送与转办人。

### 2. 审批人字段目前被废弃，真正生效的是抄送和转办
实体里虽然还有 `approveIds`，但 service 里对应逻辑几乎都注释掉了。当前 create/update 的强校验只保留：
- `ccRecipientIds` 非空
- `transferIds` 非空

也就是说，这张表现在真实承担的是“谁能被抄送、谁能转办”，而不是审批人编排。

### 3. 存储口径是逗号拼接字符串，不是关系表
create/update 时会把：
- `ccRecipientIds: List<Long>`
- `transferIds: List<Long>`

转成逗号拼接字符串写库；get/pageList 再拆回 `List<Long>`。因此它是轻量配置表，不是标准多对多关系表。

### 4. pageList 会把用户和菜单名称一起补齐
`pageList(...)` 除了分页查询，还会：
- 收集所有 `ccRecipientIds`
- 收集所有 `transferIds`
- 收集所有 `menuIds`
- 调 `adminUserApi.getUserNickNameMap(...)`
- 调 `adminUserApi.getMenuMap(...)`

把：
- `ccRecipientNames`
- `transferNames`
- `menuName`

一起补到返回值里。说明这张表的后台维护视图本身就是聚合展示，而不是只回原始 ID。

### 5. 新建款和改款的企微可见性直接依赖它
在 `NewSkuBusiness` / `UpdateSkuBusiness` 里，`copySettingService` 被直接用于：
- 判断当前登录人是否命中 `ccRecipientIds`
- 命中后再取对应 `menuId`
- 再根据菜单权限拼装“抄送人可见的状态集合”
- 获取抄送人列表 `getCcRecipient(...)`
- 获取转办人列表 `getTransfer(...)`

这说明 `copysetting` 不是外围配置，而是审批域企微页和协同入口的核心规则源。

### 6. 查询抄送配置时默认取“启用状态下、同 menuId 的最新一条”
在 `NewSkuBusiness` / `UpdateSkuBusiness` 中，取抄送人或转办人时会：
- `status = ENABLE`
- `menuId in (...)`
- `orderByDesc(id)`
- `last("limit 1")`

因此同一菜单如果存在多条启用记录，实际生效的是最新一条，而不是唯一配置。

## 风险与待确认
1. `copy_setting` 用逗号字符串存人 ID，没有关系表约束，后续用户删除、停用、改权限时容易留下脏配置。
2. 审批人字段 `approveIds` 目前基本废弃，但实体和表里还保留，容易让维护者误以为它仍在生效。
3. 新建款/改款里对抄送配置默认取“同 menuId 启用状态的最新一条”，没有显式唯一约束，可能出现多条启用配置并存。
4. 企微页里判断“当前人是否属于抄送人”用的是字符串 `contains(loginUserId)` 口径，需要确认是否存在子串误判风险。

## 结论
抄送配置应定位为“SKU 审批域协同规则主数据”。它不直接改业务单，但直接决定谁能看到新建款/改款的抄送视图、谁能被选为转办人，是审批协同链的关键配置源。
