# 维修入库明细日志模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`maintaininbilldetaillog` 不是被动日志表，而是“维修入库结果事件 + 客留单自动生成”的执行宿主。记录日志只是第一步，部分业务类型还会顺带自动生成客户留货。

## 关键入口
- Service: [MaintainInBillDetailLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/maintaininbilldetaillog/MaintainInBillDetailLogServiceImpl.java)

## 真实行为
- `logAndRemain(logParams)`
  - 先记维修入库明细日志，再自动生成客户留货。
- `createCustomerRetain(logParams)`
  - 只有客户维修单才会继续生成客留单。
- `createCustomerRetainCreateReqVO(...)`
  - 会把维修明细拆成“单件”和“成品”两组，分别落成不同 `BizTypeEnum` 的客留单。
- `createLog(logParams)`
  - 把维修单、维修明细、来源单、重量、数量和新货信息落入日志表。

## 在主链中的作用
- `maintaininbill`
  - 维修入库审核时不只是记日志，还可能继续衍生出客户留货执行链。
- `customerretain`
  - 客留单在这条链里不是人工新建，而是由维修入库日志层自动生成。

## 风险与待确认
1. `⚠️` 日志层有真实业务副作用，`logAndRemain(...)` 失败时不只是“少一条日志”，还会影响客留单生成。
2. `⚠️` 只有客户维修单才会自动生成客留，业务类型判断强依赖 `BizTypeEnum.MAINTAIN_SKU_BILL_CUSTOMER`。
3. `⚠️` 客留生成里大量直接取 `itemMap/skuMap/billMap`，若上游数据缺失，失败点会落在日志层而不是主审核链。
