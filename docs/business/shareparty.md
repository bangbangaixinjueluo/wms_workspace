# 共享方

## 模块定位
- 对应主表：`wh_share_party`
- 配套表：`wh_share_party_local`
- 入口：`SharePartyController`、`SharePartyServiceImpl`
- 这不是普通资料表，而是“共享参与方主档 + 可共享库位绑定 + 共享方库存视图”的规则宿主。

## 主数据职责
- `wh_share_party` 维护共享方主档，核心字段包括：
  - `code`
  - `name`
  - `customerId`
  - `userId`
  - `status`
- `wh_share_party_local` 维护共享方与库位的绑定关系。

## 库位配置链
- `setting(id, localIds)` 会先删后建共享方库位关系。
- 设置前会校验：
  - 共享方存在
  - 库位存在
  - 库位 `stockShare = true`
- 也就是说，共享方不是任意绑定库位，而是只能绑定已开启“库存共享”的库位。

## 查询与视图
- 共享方分页会联查：
  - 客户名称
  - 用户昵称
  - 创建人/更新人昵称
- `getLocal(id)` 返回共享方当前绑定的库位集合。
- `getStockPage(...)` 不是查共享方主档，而是直接把：
  - 共享方
  - 共享方库位
  - SKU 库存
  - 库位
  - 仓库
  - 网点
  - 大类
  - 材质
  联成一张共享方库存视图。

## 与共享库存的关系
- `SharePartyController.setting(...)` 最终并不是只调 `sharePartyService`，而是走 `ShareStockBusiness.settingPartyLocal(...)`。
- 也就是说，修改共享方库位后，会继续触发共享库存下发逻辑：
  - 新增库位对应库存会发共享消息
  - 移除库位对应库存会发取消共享消息

## 关键结论
- 共享方不是孤立主数据，而是共享库存的上游规则入口。
- `wh_share_party_local` 改动的真实影响不止“关系表变化”，还会触发共享库存消息同步。
- 共享方库存页本质上是“规则表 + 实时库存表”的联查结果，不是单表展示。

## 风险与疑点
- `⚠️` `setting(...)` 采用先删后建，若中途失败，旧绑定可能已经被清空。
- `⚠️` 控制器 `setting/{id}` 权限挂的是 `wh:share-party:query`，不是 `update`，权限粒度偏松。
- `⚠️` 创建/修改共享方本身没看到 `code` 唯一性或客户唯一性校验，需确认是否由库层或页面保证。
- `⚠️` 共享方库位调整后会继续触发共享库存消息，说明这一步是高影响配置，不是普通后台维护。
