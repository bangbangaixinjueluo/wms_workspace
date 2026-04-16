# 老庙接口调用明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`lmapitransfer` 是老庙接口调用痕迹宿主，承接接口串号、批量落库和回调更新，不是普通表。

## 关键入口
- Service: [LmApiTransferServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/lmapitransfer/LmApiTransferServiceImpl.java)

## 真实行为
- `save(apiTransfer)` / `saveBatch(apiTransferList)`
  - 承接接口调用痕迹落库。
- `getBySerialNumber(serialNumbers)` / `getOneBySerialNumber(serialNumber)`
  - 按接口串号回查调用记录。
- `update/updateBatch`
  - 承接回调后的批量回写。

## 在主链中的作用
- `lmorder/lmcallback/deliverybill/synctask`
  - 老庙协议链的接口痕迹最终沉淀在这里。

## 风险与待确认
1. `⚠️` `getOneBySerialNumber(...)` 只取 `limit 1`，若同串号存在多条记录，需要确认返回哪条才是正确语义。
2. `⚠️` service 层没有分页/查询能力，排障往往要回到 mapper 或上游业务链。
