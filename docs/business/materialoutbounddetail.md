# 客户出料单明细模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`materialoutbounddetail` 是客户/供应商出料链的行级事实层。原料重量、折算重量、结算金额、钱包类型和导出汇总，都在这层汇总和补数。

## 关键入口
- Service: [MaterialOutboundDetailServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/materialoutbounddetail/MaterialOutboundDetailServiceImpl.java)

## 真实行为
- `getMaterialOutboundDetailPage(...)`
  - 分页会先按业务员可见客户集过滤，再返回 summary。
- `exportRecycleDetailExcel(...)`
  - 导出时会补仓库、客户、账户、材质、钱包类型，并在末尾追加总计行。
- `fillData(respVO)`
  - 详情回显时补材质名称、钱包名称和钱包类型编号。
- `computeForWallet(details)`
  - 按 `materialNo -> convertWeight` 汇总，供钱包扣减链使用。
- `computeForStock(details)`
  - 按 `materialNo -> originWeight` 汇总，供原料库存扣减链使用。

## 在主链中的作用
- `materialoutbound`
  - 主单审核时需要同时扣原料库存和钱包；明细层分别提供库存口径和钱包口径。

## 风险与待确认
1. `⚠️` `fillData(...)` 里先调用了 `walletDO.getWalletName()`，后面才判断 `walletDO == null`，这里存在明显的空指针风险。
2. `⚠️` 导出列表会追加总计行，导出消费侧要区分事实行和汇总行。
3. `⚠️` `deleteMaterialOutboundDetail(...)` 仍是物理删除，未见主单状态保护。
