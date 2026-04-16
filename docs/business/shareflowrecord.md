# 共享库存流水记录模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`shareflowrecord` 是共享库存动作流水宿主，不只是发送日志。共享库存待同步、发送失败、批量补写、销售结算待同步队列，都在这层承接。

## 关键入口
- Service: [ShareFlowRecordServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/shareflowrecord/ShareFlowRecordServiceImpl.java)

## 真实行为
- `getPendingListBySaleBillNo(saleBillNos)`
  - 按销售结算单号拉共享库存待同步流水。
- `updateSendFail(id, reason)`
  - 回写发送失败原因。
- `updateBySerialNumber(record)`
  - 按串号批量更新共享流水。
- `saveBatch/batchUpdate`
  - 支持共享库存流水批量落库和批量回写。

## 在主链中的作用
- `sharestock/deliverybill/customerorder`
  - 多条共享库存与履约链最终会把同步动作沉淀成共享流水记录。

## 风险与待确认
1. `⚠️` `updateSendFail(...)` 只回写 `failReason`，没有显式同步状态字段，语义要结合调用方确认。
2. `⚠️` `updateById(...)` 返回值恒为 `null`，调用方若依赖返回值会误判。
