# 客户字印

## 模块定位
- 对应表：`wh_customer_marking`
- 入口：`CustomerMarkingController`、`CustomerMarkingServiceImpl`
- 这不是普通客户附属资料表，而是“客户默认字印 + 打印内容来源 + 订单/产品包校验规则”的宿主链。

## 主数据职责
- 关键字段包括：
  - `customerId`
  - `businessStaffId`
  - `businessAreaId`
  - `businessManageId`
  - `markingType`
  - `markingNo`
  - `content`
  - `acquiesce`
  - `status`
  - `pics`
- 建档时会从客户档案回填业务员、业务区和业务经理，不是纯前端自填。
- `markingNo` 是全局唯一识别码；`content` 则按“同客户下不可重复”校验。

## 主要业务动作
- `createCustomerMarking(...)`
  - 校验 `markingNo` 全局唯一。
  - 校验当前客户下 `content` 不重复。
  - 从客户档案补业务归属字段。
- `updateCustomerMarking(...)`
  - 同样重做编号和内容校验。
  - 若本次把 `acquiesce` 改成 `1`，会先把同客户其他默认字印全部清成 `0`。
- `deleteCustomerMarking(...)`
  - 当前是物理删除，只校验存在，不校验是否已被业务引用。
- `getSimpleList(customerId)`
  - 不是纯查询。
  - 读取前会先执行 `createDefaultMarkingIfExist(customerId)`，客户完全没有字印时会自动补一条公共默认字印。

## 默认字印补建链
- `createDefaultMarkingIfExist(customerId)`：
  - 当客户名下没有任何字印时，自动插入 `CommonMarkingEnum.COMMON_MARKING`。
  - 默认 `markingNo` 规则是“公共字印前缀 + customerId”。
- `createDefaultMarkingIfExists(customerIds)`：
  - 用于批量补齐客户默认字印。
- 这说明客户字印不是静态主数据，而是带“按需自愈”能力的配置宿主。

## 下游使用链
- `CustomerBusiness`
  - 新建客户、批量处理客户后，会补默认字印。
- `CustomerOrderReceiveBusiness`
  - 页面与详情回显时，会根据 `markingId` 补 `content` 和 `markingType`。
- `WhCustomerOrderApiImpl`
  - 小程序/外部订单提交时，若传了 `markingId`，会把字印内容写入 `customerOrder.printContent`。
- `PackBusiness`
  - 建包时会校验字印存在且启用，并把 `markingNo` 回写到包单。
- `SaleBillBusiness`
  - 销售结算链会继续承接前序字印编号。
- `WhCustomerMarkingApiImpl`
  - 对外只暴露启用字印列表。

## 关键结论
- 客户字印不是普通 CRUD，而是客户打印内容与字印编号的规则来源池。
- 订单、小程序、产品包、销售链读到的字印内容，真实都追溯到 `wh_customer_marking`。
- “默认字印”并不是人工一次性初始化，而是读链路里会自动补建。

## 风险与疑点
- `⚠️` `deleteCustomerMarking(...)` 当前是物理删除，未见“已被订单/包单/历史打印引用时禁止删除”的保护。
- `⚠️` `getSimpleList(...)` 带有“查询即补默认字印”的副作用，读接口不是纯只读。
- `⚠️` 默认字印切换只靠应用层先清其他默认值，未见数据库层唯一约束。
- `⚠️` `markingNo` 是全局唯一，而 `content` 是客户内唯一，两套唯一口径并存，后续导入或批量建档时要一起考虑。
