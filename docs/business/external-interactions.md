# 外部系统交互清单
> 当前覆盖范围：已分析的核心交易链模块；基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 说明
- 这里只记录已经从代码主链确认的外部交互。
- “请求发起成功”与“业务最终成功”必须分开记录。

## 外部交互总表
| 外部系统/能力 | 触发入口 | 请求时机 | 成功回写 | 失败回写 | 异步回调 | 幂等/关联键 | 依据文档 |
|------|------|------|------|------|------|------|------|
| 老庙出货接口 | `DeliveryBusiness.ship()` | 出货单 `WAIT_DELIVERY` 时发起发货 | `wh_delivery_bill.status -> IN_DELIVERY` | `wh_delivery_bill.status -> DELIVERY_FAIL` | 是 | `serialNumber`、出货单号、`lm_api_transfer` 流水 | [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| 老庙出货接口重试 | `DeliveryBusiness.reship()` | 出货单 `DELIVERY_FAIL` 时重试 | `wh_delivery_bill.status -> IN_DELIVERY` | `wh_delivery_bill.status -> DELIVERY_FAIL` | 是 | 复用 `LmApiTransferDO` 流水 | [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| 老庙出货回调 | `callbackYxysOrder()/callbackPPOrder()` | 外部系统异步通知 | `wh_delivery_bill.status -> DELIVERY_SUCCESS`；`wh_customer_order.order_status -> DELIVERED`；`out_order_status -> SHIPPED` | `wh_delivery_bill.status -> DELIVERY_FAIL`；`out_order_status -> DELIVERY_FAIL` | 回调本身 | 外部业务单号/流水号 | [deliverybill.md](/D:/ws/code/wms-api/docs/business/deliverybill.md) |
| BPM 延期审批 | `CustomerRetainBusiness.applyExtension()` | 客留存申请延期时发起 | 写 `processResult/processInstanceId/processUserId/applyExpireTime` | 抛异常，不落审批结果 | 是 | 业务键为客留存 ID | [customerretain.md](/D:/ws/code/wms-api/docs/business/customerretain.md) |
| BPM 展销仓调拨审批 | `TransferBillServiceImpl.confirm()` / `GoodsTransferBillServiceImpl.comfirm()` | 目标仓是展销仓时发起 | 调拨单进入 `CONFIRMING`，审批通过后再 `CONFIRM` | 审批不通过时不能进入正式调拨 | 是 | 调拨单 ID / BPM 流程实例 | [transferbill.md](/D:/ws/code/wms-api/docs/business/transferbill.md)、[goodstransferbill.md](/D:/ws/code/wms-api/docs/business/goodstransferbill.md) |

## 关键区分
1. 出货链里：
   - `ship/reship` 成功只代表“请求发出”
   - `callback...` 成功才代表“真正发货成功”
2. 调拨链里：
   - 调拨 `confirm()` 在展销仓场景可能只到 `CONFIRMING`
   - BPM 通过后才算真正审核通过
3. 客留存延期里：
   - 发起 BPM 后主状态不变
   - 只是流程字段进入处理中
