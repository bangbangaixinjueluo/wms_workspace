# SKU 编码规则
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
`skurule` 不是普通资料表，而是 SKU 正式编码生成的序列规则宿主。它按“品类代码 + 供应商代码 + 销售工费代码”三元组维护当前流水号；`skurulelog` 则沉淀每次实际落号后的结果轨迹。

## 关键入口
- Service: [SkuRuleServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skurule/SkuRuleServiceImpl.java)
- Service: [SkuRuleLogServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/skurulelog/SkuRuleLogServiceImpl.java)
- Entity: [SkuRuleDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/skurule/SkuRuleDO.java)
- Entity: [SkuRuleLogDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/skurulelog/SkuRuleLogDO.java)

## 真实行为
### 1. 规则主表按三元组维持流水号
`wh_sku_rule` 的核心字段很少，但都很关键：
- `categoryCode`
- `supplierCode`
- `saleFeeCode`
- `serialNo`

这说明 SKU 编码规则不是全局一个流水，而是按“三元组”拆分的局部序列池。

### 2. 真正的生成入口是 `genSkuRule(...)`
`genSkuRule(categoryDivideNo, supplierCode, saleFeeCode)` 的行为是：
1. 三个输入都不能为空
2. 先按三元组查规则主表
3. 若没有记录，则返回一条临时规则对象，`serialNo = SKU_SERIAL_NO_INIT`
4. 若已有记录，则把已有 `serialNo + 1`
5. 新流水统一左补零到 4 位

也就是说，这个服务本身更像“生成下一个可用流水号”的规则引擎，而不是单纯 CRUD。

### 3. 规则日志才是“实际落号轨迹”
`wh_sku_rule_log` 会额外记录：
- `skuRuleId`
- `skuId/skuNo/skuName`
- `categoryId/categoryCode`
- `supplierId/supplierCode`
- `basicGramCmtFee/moreGramCmtFee`
- `saleFeeCode`
- `serialNo`

所以 `skurule` 负责“下一个号该是多少”，`skurulelog` 负责“这个号最后给了谁、当时口径是什么”。

### 4. 规则日志把销售工费口径也固化了
日志表不只记三元组和编号，还把：
- `basicGramCmtFee`
- `moreGramCmtFee`
- `saleFeeCode`

一起落下来。这说明 SKU 编码规则和销售工费口径是耦合的，后续如果销售工费代码解释变了，历史编码轨迹仍需要靠日志还原当时语义。

### 5. CRUD 只是外围维护能力
`SkuRuleServiceImpl` 和 `SkuRuleLogServiceImpl` 都有完整的增删改查分页导出接口，但真正有业务含义的是：
- `genSkuRule(...)`
- `getSkuRuleLogBySkuNo(...)`

前者是生成序列，后者是按正式款号回查落号轨迹。

## 风险与待确认
1. `genSkuRule(...)` 只是返回“下一条规则对象”，真正把 `serialNo` 落回主表、并把日志写入 `wh_sku_rule_log` 的地方不在这个服务里，需要继续依赖调用方完整性。
2. 规则主表和规则日志表当前都支持物理删改，但它们本质上属于编码生成链，需确认业务上是否真的允许改历史规则或删历史日志。
3. 规则拆分维度依赖 `saleFeeCode`，如果销售工费编码规则调整，SKU 正式编码生成口径会一起变化。
4. `serialNo` 固定 4 位，后续同一三元组下款式数量增长后需要确认容量是否足够。

## 结论
SKU 编码规则应定位为“SKU 正式编码序列规则宿主”，规则日志则是“实际落号轨迹表”。它们共同决定正式 `skuNo` 如何生成以及历史编码如何追溯。
