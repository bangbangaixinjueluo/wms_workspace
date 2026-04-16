# 客户留货模块
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-04-01

## 模块职责
`customerretain` 不是普通记录表，而是客户留货宿主链。留货生成、迁转、打包、结算关闭、取消回收、片区权限过滤都在这层承接。

## 关键入口
- Service: [CustomerRetainServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/customerretain/CustomerRetainServiceImpl.java)

## 真实行为
- `createCustomerRetain(createReqVO)`
  - 建单时会生成 `CUSTOMER_RETAIN` 单号。
- `transfer(toRecordBO)`
  - 客留迁转时会按剩余件数/金重决定“原记录保留 + 新建进行中记录”还是直接改单状态。
- `packing(toRecordBO)` / `packingBack(packNo)`
  - 客留和产品包绑定/解绑，状态在 `STORAGE` 与 `ONGOING` 之间切换。
- `retainSettled(saleBillNo, packList)` / `retainSettledBack(...)`
  - 销售结算会把客留单结案；反向操作时又可重新激活。
- `cancel(retainId)`
  - 留货上架/回收取消时，会回写结束时间、天数和回收状态。

## 在主链中的作用
- `maintaininbilldetaillog`
  - 维修入库日志层会自动生成客户留货。
- `pack/salebill`
  - 打包和销售结算会持续回写客留状态。

## 风险与待确认
1. `⚠️` `deleteCustomerRetain(...)` 当前是物理删除，历史留货轨迹可被直接抹掉。
2. `⚠️` `transfer(...)` 的“部分迁转”采用原记录减量 + 新建新记录模式，后续排障必须同时看两条记录。
3. `⚠️` `retainSettled(...)` 与 `retainSettledBack(...)` 都强依赖 `packNo` 反查，产品包链若异常会直接影响客留状态闭环。
