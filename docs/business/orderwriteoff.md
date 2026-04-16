# 订单核销
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
负责订单核销单的创建、修改、审核、反审、作废、导出与详情回显。模块自身维护 `wh_order_writeoff` 与 `wh_order_writeoff_detail`，但真正的业务副作用主要发生在委外单和需求单：审核/反审时会同步回写委外单明细累计核销量、委外单头状态，以及需求单明细/主单的累计回货结果。

## 涉及数据表
| 表名 | 读/写 | 说明 |
|------|-------|------|
| `wh_order_writeoff` | 读写 | 核销单主表，对应 `OrderWriteoffDO` |
| `wh_order_writeoff_detail` | 读写 | 核销单明细表，对应 `OrderWriteoffDetailDO` |
| `wh_outsourcing_order` | 读写 | 审核/反审时重算委外单头累计核销量和状态 |
| `wh_outsourcing_order_detail` | 读写 | 审核/反审时累加或回退合格/不合格/回货/核销累计值 |
| `wh_demand_order` | 读写 | 审核/反审后通过明细聚合回写需求单头 |
| `wh_demand_order_detail` | 读写 | 审核/反审时累加或回退需求单明细 `returnedNum/returnedWeight` |

## 模块依赖
- `CommonService`：生成核销单号、获取登录用户名。
- `SupplierService` / `BigClassService` / `TextureService` / `ShopService`：列表导出和详情展示时补充名称。
- `OutsourcingOrderMapper` / `OutsourcingOrderDetailMapper`：校验委外单状态、回写委外明细累计值、刷新委外单头状态。
- `DemandOrderMapper` / `DemandOrderDetailMapper`：回写需求单明细累计回货并聚合主单。
- `RedissonClient`：创建单据时通过 `WRITEOFF_CREATE_TIME_LOCK` 串行化取时间。
- `AdminUserApi`：分页列表补最近更新人名称。

## 接口列表

### createOrderWriteoff
> `OrderWriteoffController.java#createOrderWriteoff()` → `OrderWriteoffServiceImpl.java#createOrderWriteoff()` `L45 / L139`

- **入口类型**：HTTP 接口
- **用途**：创建订单核销单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **并发控制**：通过 Redis 锁 `WRITEOFF_CREATE_TIME_LOCK` 串行化获取 `LocalDateTime.now()`，避免高频并发创建时间冲突 `→ L119-L127`
- **业务逻辑**：
  1. 调用 `commonService.genBillNo(BillTypeEnum.ORDER_CANCELLATION.getPrefix())` 生成核销单号 `→ L141`
  2. 初始化表头累计字段为 0，包括委外数、核销件重、回货件重、合格/不合格累计 `→ L147-L154`
  3. 进入 `handleConvertDetail()` 遍历明细：
     - 先校验涉及的委外单不能处于 `FINISH(9=终止核销)` `→ L178-L180`
     - 把每行 `returnedNum/returnedWeight` 与 `badWriteoffNum/badWriteoffGramWeight` 缺省值补 0 `→ L185-L192`
     - 计算 `passNum = returnedNum - badWriteoffNum`、`passGramWeight = returnedWeight - badWriteoffGramWeight` `→ L193-L196`
     - 再把 `writeoffNum/writeoffGramWeight` 直接写成合格件数/合格克重 `→ L197-L198`
     - 同时把每行累计汇总到表头 `→ L211-L218`
  4. 手动设置 `createTime/updateTime = getNow()` `→ L158-L160`
  5. 先写主表，再补明细 `orderId/orderNo` 后批量写入明细表 `→ L161-L170`
- **关键结论**：
  - 核销单中的“核销件数/核销克重”不是总回货量，而是“回货量减去不合格量后的合格量”。
  - 创建时不会立刻改委外单或需求单，只有审核/反审才会产生跨模块写入。

### updateOrderWriteoff
> `OrderWriteoffController.java#updateOrderWriteoff()` → `OrderWriteoffServiceImpl.java#updateOrderWriteoff()` `L53 / L228`

- **入口类型**：HTTP 接口
- **用途**：修改未审或已反审的核销单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - `id` 不能为空 `→ L231-L232`
  - 旧单状态必须满足 `BillStatusEnum.confirmCheck()`，即只允许 `INIT/INFIRM` `→ L233-L236`
- **业务逻辑**：
  1. 重新初始化表头累计字段并重新走 `handleConvertDetail()` `→ L239-L249`
  2. 更新主表 `→ L250`
  3. 删除旧明细并按当前请求全量重建明细 `→ L252-L260`
- **状态变更**：不改表头状态，只重建内容。

### confirm
> `OrderWriteoffController.java#confirm()` → `OrderWriteoffServiceImpl.java#confirm()` `L103 / L392`

- **入口类型**：HTTP 接口
- **用途**：审核核销单，并把核销结果回写到委外单和需求单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 当前核销单状态必须是 `INIT/INFIRM`，通过 `BillStatusEnum.validConfirm()` 校验 `→ L394`
  - 明细不能为空 `→ L395-L396`
  - 每行“回货/不合格”相关字段不能同时全为 0；代码按 `returnedNum`、`badWriteoffNum`、`returnedWeight`、`badWriteoffGramWeight` 四个字段做 OR 校验 `→ L397-L400`
- **业务逻辑**：
  1. 调用 `refreshOutsourcingOrder(order, detailList, CONFIRM)` 回写委外链 `→ L402`
  2. 调用 `writeOffDemandOrder(order, detailList, CONFIRM)` 回写需求单链 `→ L404`
  3. 把核销单头状态更新为 `CONFIRM(2)`，并写入 `approvedMan/approvedTime` `→ L406-L414`
- **跨模块回写**：
  - 委外单明细：
    - `totalPassNum/totalPassGramWeight += 本次合格核销量`
    - `totalBadNum/totalBadGramWeight += 本次不合格量`
    - `totalReturnNum/totalReturnWeight += 本次回货量`
    - `totalWriteoffNum/totalWriteoffGramWeight += 本次核销量`
    `→ L492-L523`
  - 委外单头：
    - `writeoffDate = 核销日期`
    - `totalDeliveryNum/totalDeliveryGramWeight = 所有明细 totalWriteoff 累加`
    - 若全部明细 `totalWriteoffNum >= outsourcingNum - finishNum`，则 `orderStatus -> DELIVERED(8=核销完毕)`
    - 否则只要已有核销量且原状态不是部分核销，则 `orderStatus -> CANCELLATION(7=已部分核销)`
    - 若总核销量回到 0，则 `orderStatus -> CONFIRM(2=待核销)`
    `→ L543-L577`
  - 需求单明细：
    - `returnedNum/returnedWeight += writeoffNum/writeoffGramWeight`
    - 注意这里加的是“合格核销量”，不是总回货量 `→ L431-L444`
  - 需求单头：
    - 调 `demandOrderMapper.aggregateByDetails(demandDetailList)` 聚合明细后批量更新 `→ L456-L460`
    - 审核场景下额外写 `writeoffDate = now()` `→ L458-L460`

### infirm
> `OrderWriteoffController.java#infirm()` → `OrderWriteoffServiceImpl.java#infirm()` `L121 / L584`

- **入口类型**：HTTP 接口
- **用途**：反审已审核核销单，回退所有跨模块累计值。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：当前状态必须为 `CONFIRM`，通过 `BillStatusEnum.infirmCheck()` 校验 `→ L586`
- **业务逻辑**：
  1. 读取当前单据明细 `→ L587-L588`
  2. 调用 `refreshOutsourcingOrder(order, detailList, INFIRM)`，把委外明细与委外单头累计量反向扣减 `→ L591`
  3. 调用 `writeOffDemandOrder(order, detailList, INFIRM)`，把需求单明细 `returnedNum/returnedWeight` 反向扣减并重新聚合主单 `→ L593`
  4. 把核销单头状态更新为 `INFIRM(3)`，同时清空 `approvedMan/approvedTime` `→ L595-L603`

### discard
> `OrderWriteoffController.java#discard()` → `OrderWriteoffServiceImpl.java#discard()` `L130 / L606`

- **入口类型**：HTTP 接口
- **用途**：作废未审核的核销单。
- **事务边界**：`@Transactional(rollbackFor = Exception.class)`
- **前置条件**：
  - 单据必须存在 `→ L609-L610`
  - 只禁止作废已审核状态；若已审核则直接拒绝 `→ L611`
- **业务逻辑**：
  1. 以“当前状态匹配”作为更新条件，把 `orderStatus -> DISCARD(4)` `→ L612-L614`
  2. 不回写委外单和需求单，因为未审核前本就没有产生跨模块累计副作用

### saveAndConfirm
> `OrderWriteoffController.java#saveAndConfirm()` → `OrderWriteoffServiceImpl.java#saveAndConfirm()` `L113 / L654`

- **入口类型**：HTTP 接口
- **用途**：一键保存并审核。
- **业务逻辑**：
  1. `id` 为空时先 `createOrderWriteoff()` `→ L657-L659`
  2. `id` 不为空时先 `updateOrderWriteoff()` `→ L659-L661`
  3. 再统一调用 `confirm(res)` 触发审核回写 `→ L662`

### listByWriteOff
> `OrderWriteoffController.java#listByWriteOff()` → `OrderWriteoffServiceImpl.java#listByWriteOff()` `L140 / L667`

- **入口类型**：HTTP 接口
- **用途**：查询“可用于核销”的委外单明细，供建单选择。
- **筛选范围**：
  - 只返回委外单状态为 `CONFIRM(2=待核销)` 或 `CANCELLATION(7=已部分核销)` 的明细 `→ L681`
  - 可按供应商、品类、委外单号、行号、款号、规格、客户、业务员等过滤 `→ L682-L688`
- **结论**：
  - 订单核销模块不是直接从结算单选数据，而是从“待核销/部分核销”的委外单明细选数据。

### get / page / export
> `OrderWriteoffController.java#get()` / `#getPage()` / `#exportOrderWriteoffExcel()` `L61 / L78 / L85`

- **用途**：
  - `get(id)`：返回表头、明细、供应商/SKU/客户/材质/大类补充信息，并计算“截止当前单据创建前”的累计核销汇总 `→ L707-L779`
  - `page()`：可通过 `outsourcingOrderNo`、`demandOrderNo` 先反查明细，再筛主单 `→ L275-L326`
  - `export-excel()`：导出时补供应商/网点/大类/材质名称 `→ L329-L389`
- **补充规则**：
  - `get(id)` 中的 `selectCutoffSum(order.getCreateTime(), outsourcingDetailIds)` 只统计当前单据创建前的累计值，因此详情页里的 `cutoff*` 是“截止本单之前”的历史累计，而不是含本单的最终累计 `→ L750-L763`

## 公共规则

### 核销量与回货量不是同一个概念
1. `returnedNum/returnedWeight` 表示本次回货总量。
2. `badWriteoffNum/badWriteoffGramWeight` 表示其中不合格量。
3. `passNum/passGramWeight = returned - bad`。
4. `writeoffNum/writeoffGramWeight` 被代码直接赋值为 `passNum/passGramWeight`，所以“核销量 = 合格量”，不是“总回货量”。

### 审核后跨模块的累计口径
1. 委外单明细同时累计合格、不合格、回货、核销四类值。
2. 需求单明细只累计 `writeoffNum/writeoffGramWeight`，也就是“合格核销量”，不会累计不合格或总回货量。
3. 委外单头状态由“全部明细累计核销量是否达到 `outsourcingNum - finishNum`”决定：
   - 全部达到：`DELIVERED`
   - 只达到部分：`CANCELLATION`
   - 全部回退到 0：`CONFIRM`

### 审批接口未真正落地
1. 模块声明了 `PROCESS_KEY = "oa_orderWriteoff"`。
2. 也实现了 `OrderWriteoffApiImpl#getProcessKey()`。
3. 但 `updateProcessResult()` 内部真实回调被整段注释，当前看不到 BPM 审批结果会自动驱动业务单状态。

## 当前结论
- 订单核销模块是“委外生产链”的结果回写节点，不直接承接已完成的出库结算单，而是承接委外单与需求单之间的核销闭环。
- 它的审核/反审不是简单改单头状态，而是显式重算委外单明细累计量、委外单头状态，以及需求单的累计回货字段。
- `wh_order_writeoff_detail.writeoff_*` 与 `wh_order_writeoff.total_*` 的业务含义都偏向“合格核销量”，后续若把它误当作“总回货量”修改，会直接影响需求单与委外单的累计结果。

## 集中待办和回填
| 标记 | 来源方法 | 目标模块 | 摘要 | 当前状态 | 回填动作 |
|------|---------|---------|------|----------|----------|
| ⚠️ | `OrderWriteoffApiImpl.updateProcessResult` | BPM / 订单核销 | 已暴露 `PROCESS_KEY = oa_orderWriteoff`，但审批回调实现被整段注释，当前看不到流程结果如何回写业务单 | 待确认 | 确认流程审批是否已废弃，或真实回调是否迁移到其他入口 |
