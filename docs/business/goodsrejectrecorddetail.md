# 成品退厂登记明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`goodsrejectrecorddetail` 是退厂登记池的行级事实层。它本身 service 很薄，但承担的是“可退厂池”明细和后续批量占用/回写基础。

## 关键入口
- Service: [GoodsRejectRecordDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/goodsrejectrecorddetail/GoodsRejectRecordDetailServiceImpl.java)

## 真实行为
- `deleteByBillId(id)`
  - 按登记单头整批物理删除明细。
- `getListByRecordIds(recordIds)`
  - 批量取退厂登记池明细。
- `updateBatch(list)`
  - 批量更新明细，供后续占用量或状态调整使用。

## 在主链中的作用
- `goodsrejectrecord`
  - 退厂登记池单头的真实可退厂明细承接在这里。
- `goodsrejectbill`
  - 正式退厂单会消费登记池明细。

## 风险与待确认
1. `⚠️` `deleteByBillId(...)` 和 `deleteGoodsRejectRecordDetail(...)` 都是物理删除，未见登记池状态保护。
2. `⚠️` 服务层本身很薄，真实占用/释放语义分散在主业务层，排障不能只看这里。
