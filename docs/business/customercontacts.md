# 客户联系人

## 模块定位
- 入口：`CustomerContactsController`、`CustomerContactsServiceImpl`
- 宿主表：`wh_customer_contacts`
- 这不是普通联系人资料表，而是“客户联系人主档 + 小程序登录入口 + 临时联系人二维码 + 客户选择绑定”复合宿主。

## 关键入口
- `/wh/customer-contacts/create`
- `/wh/customer-contacts/login`
- `/wh/customer-contacts/tmpLogin`
- `/wh/customer-contacts/setCustomer`
- `/wh/customer-contacts/bindPhone`
- `/wh/customer-contacts/createMinProgramQrCode`

## 真实行为
- 建档时默认 `type=1`；若 `type=2`，会调用 `wxMaService` 生成临时联系人小程序码，并通过 `fileApi` 上传后回写 `miniLoginCode`。
- 临时联系人默认有效期被补到当天 `23:59:59`。
- `login(code)` 会走微信 `jscode2session`，再按 `openId` 查联系人；若命中一个联系人，直接创建 OAuth2 token；若命中多个联系人，则返回 `customerMap` 让前端二次选客。
- `tmpLogin(customerContactsId)` 只允许临时联系人登录，并校验未过期。
- `setCustomer(...)` / `bindPhone(...)` 本质上都是把本次微信登录态绑定到联系人，再签发系统 token。
- 登录成功后还会写系统登录日志。

## 在主链中的作用
- 给客户侧小程序提供身份入口。
- 给“一个微信对应多个客户”的场景提供二次选客绑定。
- 给临时联系人提供二维码分享和临时访问入口。

## 关键结论
- 这张表既是客户联系人主档，也是客户侧登录账户锚点。
- `openId`、`expireTime`、`miniLoginCode` 这些字段已经把它从“主数据”推成了“认证入口层”。

## 风险与待确认
- `⚠️` `deleteCustomerContacts(...)` 仍然是物理删除，未见对历史登录、二维码和客户侧引用的保护。
- `⚠️` `getByOpenId(openId)` 先查了带过期条件的一版结果，但最终直接返回了第二次裸查询结果；临时联系人过期过滤看起来可能没有真正生效。
- `⚠️` 临时联系人二维码生成失败直接抛 `CUSTOMER_CONTACTS_CREATE_MINI_QR_FAIL`，没有降级补救链。
