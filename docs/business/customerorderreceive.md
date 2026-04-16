# 客户订单接单

## 模块定位
- 入口：`CustomerOrderReceiveController`、`CustomerOrderReceiveBusiness`
- 宿主表仍是：`wh_customer_order`、`wh_customer_order_detail`
- 这不是独立订单表，而是客户订单域里的“业务接单入口层”，主要承接业务员建单、改单、提交、接单、拒单和转需求单。

## 与客户订单主链的边界
- [customerorder.md](/D:/ws/code/wms-api/docs/business/customerorder.md) 讲的是订单域宿主。
- 这一层讲的是“业务员如何把订单推进到可拣货/待回货”的前置操作流。
- 它本身不承接老庙出货回调，也不直接承接共享库存同步。

## 主要业务动作
- `createByStaff(...)`
  - 生成内部 `orderNo`
  - 从客户主档补业务员、业务区、业务经理
  - 校验明细 `(skuNo + modelNo + specs + bigClassId + textureId)` 不重复
  - 汇总 `totalNum`
  - 保存单头和款式明细
- `updateByStaff(...)`
  - 校验订单存在
  - 重算总件数
  - 先删全部旧明细再整单重写
- `submit(id)`
  - 只允许 `UN_SUBMIT`
  - 且必须存在明细
  - 提交后把订单推进到 `WAIT_PICKING`
- `taking(id)`
  - 真正的“业务接单”
  - 只允许 `WAIT_TAKING`
  - 接单后推进到 `WAIT_PICKING`
  - 同时发订单消息、回写业务员/柜员侧消息状态
- `reject(id)`
  - 只允许 `WAIT_TAKING`
  - 拒单后直接切 `DISCARD`
- `toDemandOrder(id)`
  - 只允许 `WAIT_PICKING`
  - 推进到 `WAIT_COMEBACK`
  - 并触发“通知进入生产中”这类消息侧动作
- `cargoReceived(id)`
  - 只允许 `WAIT_COMEBACK`
  - 收货确认后回到 `WAIT_PICKING`

## 状态链重点
- 这层真正处理的主要是订单前置态：
  - `UN_SUBMIT`
  - `WAIT_TAKING`
  - `WAIT_PICKING`
  - `WAIT_COMEBACK`
  - `DISCARD`
- 也就是说它覆盖的是“建单 -> 提交 -> 接单/拒单 -> 转需求 -> 回货确认”这一段。

## 与明细/标签的关系
- 这层保存的是款式明细 `customer_order_detail`。
- 现场挑货的单件锁定与 `customer_order_item` 更多落在 [CustomerOrderIntentBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/CustomerOrderIntentBusiness.java)。
- 所以接单层偏“业务订单头与款式计划”，挑货层偏“单件执行”。

## 关联模块
- `customerorder`：订单域宿主。
- `demandorder`：`toDemandOrder(...)` 的下游去向。
- `customerorderitem`：后续挑货与状态重算依赖的单件层。
- `customermarking`：详情页会补字印信息。

## 关键结论
- 客户订单接单不是单独业务单，而是客户订单域的前置流转入口。
- 它最重要的价值是把业务员侧动作和订单前置状态机固定下来，而不是承接后面的履约或同步。

## 风险与疑点
- `⚠️` `updateByStaff(...)` 当前是“先删全部旧明细再重写”，若后续已有下游引用，整单覆盖风险较高。
- `⚠️` `submit(...)` 提交后直接进 `WAIT_PICKING`，而不是 `WAIT_TAKING`，需结合不同建单来源确认业务语义是否统一。
- `⚠️` `toDemandOrder(...)` 只改订单状态并发通知，真实需求单生成依赖下游 `DemandOrderService` 口径，需要继续核对是否一定同步生成。
