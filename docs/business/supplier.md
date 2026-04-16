# 供应商档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
供应商档案不是审批型业务单，而是“供应商主档 + 财务账户绑定 + 开放钱包删除保护 + 导入更新 + 变更日志”的基础主数据模块。和客户档案不同，它当前没有 BPM 缓存审批链，建档后直接生效。

## 关键入口
- Controller: [SupplierController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/supplier/SupplierController.java)
- Service: [SupplierServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/supplier/SupplierServiceImpl.java)

## 真实行为
### 1. 建档时就直接绑定财务账户
`createSupplier(...)` 会先校验：
1. `supplierNo` 唯一。
2. `supplierCode` 唯一。
3. `supplierName` 唯一。

之后立即处理账户链：
- 若传了 `accountCode`：
  - 存在则直接绑定该账户；
  - 不存在则自动新建供应商账户。
- 若没传 `accountCode`：
  - 自动生成账户编码；
  - 自动创建供应商账户。

也就是说，供应商档案从创建起就是“主档 + 账户”一起落地，而不是先主档、后审批。

### 2. 更新时会同步更新账户信息
`updateSupplier(...)` 不只是改主表，还会：
1. 校验新 `supplierNo/supplierCode/supplierName` 不和别人冲突。
2. 根据 `accountCode` 重新找到目标账户。
3. 校验该账户没有被别的供应商占用。
4. 如账户编码、类型、名称变化，则同步更新 `wh_account`。
5. 记录供应商变更日志。

因此供应商名称、账户编码、账户类型都属于同一条变更事务链。

### 3. 删除前会拦开放钱包，但不会删账户
`deleteSupplier(...)` 会校验：
1. 供应商存在。
2. 关联账户存在。
3. 该账户下不存在 `status = OPEN` 的钱包。

通过后只执行：
- 删除 `wh_supplier`

当前没有继续删除 `wh_account`。

### 4. 状态是独立启停字段，不带审批语义
`updateSupplierStatus(...)` 只是直接改 `status`，没有 BPM、没有缓存、也没看到联动账户或钱包状态。它更像主档启停开关，而不是审批态。

### 5. 导入支持“新增或覆盖”，并带账户联动
`importExcel(file, updateSupport)` 会：
1. 过滤空编号。
2. 预加载现有供应商与账户。
3. 校验编号、财务代码、供应商代码、名称在导入文件内不重复。
4. 校验 `supplierCode` 非空时必须是 2 位数字。
5. 校验供应商类型必填。
6. 对每行数据同步创建或更新财务账户。
7. 若名称匹配或编号匹配且允许更新，则改旧供应商；否则插入新供应商。

新导入新增的供应商会直接写成 `ENABLE`。

## 关键实体
- `wh_supplier`：供应商主档。
- `wh_account`：供应商绑定的财务账户。
- `wh_wallet`：删除前的阻断引用。
- `change_log`：供应商变更日志。

## 风险与待确认
1. `deleteSupplier(...)` 删除供应商时没有同步删除账户，也没看到把账户标成停用，容易留下悬空 `wh_account`。
2. 删除保护只拦开放钱包，没看到对来料、出料、维修、委外、成品入库等历史业务引用的保护。
3. `updateSupplierStatus(...)` 只改供应商状态，没有联动账户或钱包状态，需要确认业务上是否允许“供应商停用但账户仍启用”。
4. `getAccount(...)` 走的是 `accountService.getCustomerByAccountCode(...)`，虽然底层可能通用，但命名上容易误导。

## 结论
供应商档案应定位为“供应商主数据 + 财务账户绑定宿主”。它没有客户档案那种 BPM 生效链，但对账户、钱包、导入和后续多条供应商业务链都是根节点。后续凡是分析“供应商账户是谁创建的”“删除为什么删不掉”“供应商改名会不会带到账户”，都要回到这里。
