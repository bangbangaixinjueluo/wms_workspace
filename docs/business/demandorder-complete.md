# 需求单完结规则
> 更新日期：2026-04-15

## 适用范围
- 客单需求页面：`wms-web/src/views/wh/order/demandOrder/*`
- 柜货订单页面：`wms-web/src/views/wh/order/demandOrderContainer/*`

## 业务目标
- 在“进行中”的需求单上增加“完结”动作。
- 只有具备权限的用户可以看到并执行“完结”。
- 完结后，剩余未完成件数转入取消件数，未到柜件数和未到柜克重归零。
- 只有对应委外单据已经“核销完毕”或“终止核销”后，需求单才允许出现“完结”按钮。

## 权限
- 权限标识：`wh:demand-order:complete`
- 前端通过按钮权限和 `canComplete` 双重控制显示。
- 后端接口也会再次校验，防止绕过前端直接调用。

## 按钮显示条件
- 需求单主状态必须是 `DemandOrderStatusEnum.ALL_OUT(6)`。
- 必须存在关联委外单明细。
- 关联的所有委外单主状态都必须满足以下之一：
  - `OutSourcingOrderStatusEnum.DELIVERED(8)`：核销完毕
  - `OutSourcingOrderStatusEnum.FINISH(9)`：终止核销

## 完结后的数据处理
- 接口：`PUT /wh/demand-order/complete/{id}`
- 对每条需求单明细计算：
  - `remainingNum = demandNum - arrivedNum - cancelNum`
- 当 `remainingNum > 0` 时：
  - `cancel_num += remainingNum`
  - `cancel_date = 当前时间`
  - 重新计算 `overdue_days`

## 未到柜数据为什么会归零
- 列表上的“未到柜件数 / 未到柜克重”不是单独存储字段，而是聚合结果：
  - `未到柜件数 = demandNum - cancelNum - arrivedNum`
  - `未到柜克重 = 平均克重 * max(demandNum - cancelNum - arrivedNum, 0)`
- 所以完结时把剩余数量转入 `cancelNum` 之后，这两项会自然归零。

## 状态口径
- 本次完结明确使用已有状态：`DemandOrderStatusEnum.COMPLETED(8)`。
- 注意：系统原有通用聚合规则中，如果整单数量都进入取消数，会被聚合成 `CANCELED(9)`。
- 为了满足“人工点击完结”的业务语义，本次实现会在汇总后显式覆盖为：
  - `order_status = COMPLETED(8)`
  - `return_status = ARRIVED_COUNTER(6)`

## 代码落点
- 后端：
  - `wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/demandorder/DemandOrderController.java`
  - `wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/demandorder/DemandOrderService.java`
  - `wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/demandorder/DemandOrderServiceImpl.java`
  - `wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/demandorder/vo/DemandOrderRespVO.java`
- 前端：
  - `wms-web/src/api/wh/order/demandOrder.js`
  - `wms-web/src/views/wh/order/demandOrder/index.vue`
  - `wms-web/src/views/wh/order/demandOrder/detail.vue`
  - `wms-web/src/views/wh/order/demandOrderContainer/index.vue`
  - `wms-web/src/views/wh/order/demandOrderContainer/detail.vue`
- 权限 SQL：
  - `wms-api/sql/update/V1.3.9.3/update_260415_demand_order_complete.sql`
