# 客户款式

## 模块定位
- 对应表：`wh_customer_sku`
- 入口：`CustomerSkuController`、`CustomerSkuServiceImpl`
- 这不是简单的“客户-SKU 关系表”，而是当前在用的客户款式主链，承接客户款号、客户侧工费、直采标记、规格重量区间、导入和权限过滤。

## 主数据职责
- 以 `customerId + customerSkuNo + status=ENABLE` 做启用态唯一约束。
- 关系建档时会反查正式 `wh_sku` 和 `wh_customer`，把 `skuNo/customerId` 固化到客户款式主表。
- `saleGramCmtFee = basicGramCmtFee + moreGramCmtFee`，属于建档时同步计算出的客户侧销售工费口径。
- `isDirect` 表示客户款是否直采，后续可被按客户款号批量反查。

## 查询与组装
- 分页前会先做业务员客户权限过滤：`customerService.getBizStaffCustomerIds(loginUserId)`。
- 还支持按客户经营区域、业务员、客户名、省市、SKU 名、大类/子类、营销名、销售件数区间做交叉筛选。
- 列表回显时会继续补：
  - 客户名称、业务分区、业务员
  - 正式 SKU 冗余字段
  - 设计元素编号转名称
  - 销售件数统计
  - 若有 `wh:customer-sku:accessories` 权限，还会继续补正式供应商 BOM 和配件视图

## 导入链
- `importDetail(file)` 不是薄导入。
- 会校验：
  - 客户款号、客户名、款式编码、是否直采必填
  - 导入文件内客户款号重复
  - 客户存在且启用
  - 同一客户下客户款号不可重复
- 导入时会把 `skuNo -> skuId`、`customerName -> customerId` 做映射，并重算客户侧工费与重量区间。

## 对外能力
- `getMapByNos(...)`：按客户 + 客户款号批量取主档。
- `getDirectByOutSkuNos(...)`：按客户款号反查直采客户款。
- `getBySkuIds(...) / getListBySkuIds(...)`：按正式 SKU 反查客户款映射。

## 关键字段
- `customerSkuNo`：客户侧款号，当前主唯一业务键之一。
- `customerSkuName`：客户侧款名。
- `basicGramCmtFee / moreGramCmtFee / moreItemCmtFee`：客户侧工费输入。
- `saleGramCmtFee`：运行时由基础克工费和附加工费聚合出的销售克工费。
- `specSize / minWeight / maxWeight`：客户视角规格和重量边界。
- `isDirect`：客户直采标记。
- `status`：仅有启停语义，删除接口实际走停用。

## 状态/动作
- 创建：插入启用态客户款式。
- 修改：重校唯一性后覆盖主表。
- 删除：控制器叫 `delete`，实际执行 `disable(id)`，不是物理删除。
- 查询/导出：都基于启用态数据。

## 关联模块
- `customer`：客户存在性、状态、业务员权限来源。
- `sku`：正式款式主档来源。
- `salebill`：销售件数统计筛选来源。
- `suppliersku / supplierskuaccessories`：有权限时补正式 BOM 视图。
- `designelements`：设计元素编号转名称。

## 风险与疑点
- `⚠️` 控制器 `/delete` 实际只是停用，不是删除，接口语义和真实动作不一致。
- `⚠️` `fillData(...)` 通过 `skuNo` 反查正式 SKU，再把正式 SKU 字段拷回客户款视图，若 `skuNo` 变化或重复，展示口径容易漂移。
- `⚠️` 设计元素分隔兼容逻辑依赖运行时替换，历史脏数据兼容性要持续确认。
- `⚠️` 有 BOM 权限时列表会联动正式供应商 BOM；客户款详情页里看到的配件结构并不属于 `wh_customer_sku` 自身，而是借用正式 BOM 视图。
