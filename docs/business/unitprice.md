# 单价信息模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`unitprice` 是正式单价主档，不是字典。钱包类型 + 结算类对应的正式价格最终沉淀在这里。

## 关键入口
- Service: [UnitPriceServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/unitprice/UnitPriceServiceImpl.java)

## 真实行为
- `getUnitPriceByWalletTypeNo(walletTypeNo, settleClass)`
  - 按钱包类型和结算类取正式单价。
- `getUnitPriceMap()`
  - 输出 `walletTypeNo,settleClass -> unitPrice` 全量映射。
- `getGoldUnitPrice()`
  - 固定按 `walletTypeNo=00, settleClass=1` 取金料价格，失败时返回 `0`。

## 在主链中的作用
- `unitpricemaintain`
  - 维护单负责驱动这里的正式价格更新。
- 多条财务/留货/结算链
  - 读取正式单价进行价格计算。

## 风险与待确认
1. `⚠️` `deleteUnitPrice(...)` 当前仍是物理删除，未见引用保护。
2. `⚠️` `getGoldUnitPrice()` 失败时直接返回 `0`，若上游没兜底会把价格计算拉歪。
