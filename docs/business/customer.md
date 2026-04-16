# 客户档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
客户档案不是简单主数据 CRUD，而是“客户主档 + 财务账户绑定 + BPM 审批 + 商城账号生成 + 业务员归属 + 地址与名称变更回写”的复合主数据模块。顶级客户会经过审批缓存链；子客户则走简化建档链，不参与 BPM，也默认不单独建账户。

## 关键入口
- Controller: [CustomerController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/customer/CustomerController.java)
- Business: [CustomerBusiness.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/biz/CustomerBusiness.java)
- Service: [CustomerServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/customer/CustomerServiceImpl.java)

## 真实行为
### 1. 建档默认先落“未启用 + 未提交”
`create(...)` 会先校验：
1. `customerNo` 唯一。
2. `customerName` 唯一。
3. `autoencoder` 唯一。
4. 顶级客户需要绑定或自动创建财务账户；子客户不建账户。

然后统一把客户先建成：
- `status = DISABLE`
- 顶级客户进入 `biz_cache` 暂存审批版业务数据
- 子客户若 `level > 1`，直接结束，不走缓存审批链

### 2. 顶级客户审批通过后才真正“启用”
`submitProcess(...)` / `batchSubmit(...)` 会发起 BPM，并把客户改成：
- `processResult = PROCESS`
- 回写 `processInstanceId`
- 回写 `processUserId`

`updateProcessResult(...)` / `updateBatchProcessResult(...)` 审批通过后会：
1. 从 `biz_cache` 取待生效版本。
2. 回写 `processResult = APPROVE`。
3. 首次通过时把 `status` 切到 `ENABLE`。
4. 回写 `approvedMan/approvedTime`。
5. 调 `accountService.createOrUpdateAccount(...)` 激活或更新客户账户。
6. 初始化客户默认字印。
7. 同步需求单里冗余的 `customerName`。
8. 若是已审批客户的变更，还会记录变更日志，并同步账户名、钱包名。

也就是说，客户档案真正的“生效版本”不是创建时那条主表，而是审批通过后从 `biz_cache` 覆盖回来的版本。

### 3. 修改客户后不一定立即生效
`update(...)` 分两类：
- 子客户：直接更新主表。
- 顶级客户：
  - 若从未审批通过过，直接更新主表并覆盖缓存。
  - 若已经审批通过过，只把主表改成 `UN_COMMIT`，新版本内容主要写进 `biz_cache` 等待再次审批。

这说明客户档案是“主表当前生效态 + 缓存待审批态”双轨结构。

### 4. 账户链不是附属字段，而是客户主链组成部分
`buildAccount(...)` / `fillAccount(...)` / `setAccount()` 说明客户和 `wh_account` 是强绑定关系：
- 顶级客户建档时可手工指定 `accountCode`，不存在就自动新建。
- 不指定时自动生成账户编码。
- `setAccount()` 还能批量给历史缺失账户的客户补账户。
- 审批通过时会再次激活/更新账户状态与名称。

### 5. 商城账号生成依赖客户启用状态
`generateAccount(customerId)` 不是财务账户生成，而是商城账号生成。它要求：
- 客户存在。
- 客户 `status = ENABLE`。

生成后回写 `mallAccount`；顶级客户默认 `revealPrice = 1`，子客户默认 `0`。

### 6. 删除不是裸删，有两道前置校验
`delete(id)` 删除前会校验：
1. 不能存在下级客户。
2. 不能存在客户钱包。

通过后才会：
- 删客户主表
- 删关联财务账户

### 7. 业务归属和地址修改都带业务副作用
- `allotBusinessStaff(...)` 会批量改客户业务员，并记录客户业务员变更流水。
- `changeAddress(...)` 只允许已审批客户修改地址，且会写变更日志。
- `filter(...)` / `getBizStaffCustomerIds(...)` 说明客户还是业务员、业务区、角色权限过滤的核心维度。

### 8. 子客户是一条独立的“继承式建档链”
`importSub(...)` 会：
1. 按父客户编码找顶级客户。
2. 禁止在子客户下继续挂子客户。
3. 复制父客户的业务区、业务员、区域编码、客户类型等核心字段。
4. 直接把子客户设成：
   - `processResult = APPROVE`
   - `level = 2`
   - `approvedTime = now`
5. 子客户编号走 `Z + 7位流水`

所以子客户本质是“继承父客户业务属性的免审批扩展档案”。

## 关键实体
- `wh_customer`：客户主档，兼管审批、启停、业务归属、商城账号等。
- `wh_account`：顶级客户绑定的财务账户。
- `biz_cache`：顶级客户待审批版本缓存。
- `wh_wallet`：客户删除前的阻断引用之一，且审批通过后账户名变更会同步到钱包名。
- `wh_demand_order`：客户名冗余回写目标。
- `change_log`：客户名称/地址等变更日志落点。

## 风险与待确认
1. `delete(...)` 只拦“子客户存在”和“钱包存在”，没看到对需求单、销售单、退货单、维修单等历史业务引用的删除保护。
2. 顶级客户更新后，已审批客户的变更主要写进 `biz_cache`，主表只改 `processResult = UN_COMMIT`；如果有旁路接口直接读主表字段，可能会看到旧数据。
3. `batchSubmit(...)` 没看到显式限制“仅顶级客户可批量提审”，但单提审 `submitProcess(...)` 有 `level = 1` 校验，需要确认批量提审是否也应补同样限制。
4. 商城账号方法名 `generateAccount(...)` 和财务账户生成语义重名，接口层容易误判。

## 结论
客户档案应定位为“客户主数据宿主链”，不是普通资料表。它上承 BPM 审批和账户建档，下接钱包、需求单、商城客户、业务员权限过滤与客户变更日志。后续凡是分析“客户名为什么没同步”“客户账户/钱包名为什么变”“为什么子客户不走审批”，都要回到这个模块。
