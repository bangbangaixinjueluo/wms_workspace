# 需求单回货状态补充：已完结

> 更新日期：2026-04-16

## 业务规则

- 点击需求单“完结”后：
  - `orderStatus = 已完成`
  - `returnStatus = 已完结`

- 这里的“已完结”不是“已到柜”的别名，而是一个新的回货状态。

## 为什么要新增

- `已到柜` 只表示回货链路已经到柜。
- `已完结` 表示用户执行了人工完结动作，剩余未完成件数已经转入取消件数，整张需求单按业务规则正式收口。

所以两者语义不同，不能继续共用同一个状态值。

## 状态口径

- 字典类型：`return_status`
- 新增状态值：`9`
- 新增状态文案：`已完结`

## 代码落点

- 后端枚举：
  - `wms-api/yudao-module-wh/yudao-module-wh-api/src/main/java/cn/iocoder/yudao/module/wh/enums/basic/order/ReturnStatusEnum.java`
- 完结回写：
  - `wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/demandorder/DemandOrderServiceImpl.java`
- 数据升级：
  - `wms-api/sql/update/V1.3.9.3/update_260416_demand_order_return_finished.sql`
- 前端展示：
  - `wms-web/src/views/wh/order/demandOrder/index.vue`
  - `wms-web/src/views/wh/order/demandOrderContainer/index.vue`
