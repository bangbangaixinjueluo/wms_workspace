# 客户款式库

## 模块定位
- 对应表：`wh_sku_customer`
- 入口：`SkuCustomerController`、`SkuCustomerServiceImpl`、`WhSkuCustomerApiImpl`
- 这是一个更早期、更轻量的客户-SKU 关系索引层，职责明显弱于 `customersku`。

## 现状判断
- 实体 `SkuCustomerDO` 已标注 `@Deprecated`。
- 主表字段很少，只保留客户、SKU、客户款号/名称、备注和少量工费字段。
- 没有 `status`、没有导入链、没有复杂回显、没有正式客户款式那套交叉过滤能力。

## 主流程
- 创建时支持一批 `skuIds` 批量挂到同一个客户下。
- 每个 SKU 都会：
  - 校验正式 SKU 存在
  - 校验同一客户下 `skuId` 不重复
  - 把正式 `skuNo/skuName` 复制到 `customerSkuNo/customerSkuName`
- 更新是单行直接改。
- 删除是物理删除。

## 查询与回显
- 分页和导出都会先套用业务员可见客户集。
- 详情回显时补：
  - 客户名称
  - 正式 SKU 编码和名称
  - 正式 SKU 图片
- `WhSkuCustomerApiImpl` 也直接依赖这张表给外部返回客户-SKU 关系 DTO。

## 对外能力
- `getSkuCustomerByCustomerSkuNo(...)`
- `getByCustomerIdAndSkuId(...)`
- `getListByCustomerIds(...)`
- `getListByCustomerIdAndSkuIds(...)`
- `WhSkuCustomerApiImpl.getListByCustomerIds(...)`

## 关键结论
- 这层不是当前最完整的客户款式宿主，而是“轻量客户-SKU 索引 + API 输出层”。
- 它和 `customersku` 存在明显职责重叠，但字段和能力都弱很多。
- 外部接口目前仍在直接依赖它，所以短期内不能简单删除。

## 风险与疑点
- `⚠️` `SkuCustomerDO` 已 `@Deprecated`，但 API 侧仍直接用它对外出数，说明这是“逻辑上弱化、依赖上仍存活”的旧链。
- `⚠️` 创建时把正式 `skuNo/skuName` 直接复制到客户字段，后续正式 SKU 改名后这里不会自动回刷。
- `⚠️` 删除是物理删除，没看到历史业务引用保护。
- `⚠️` `checkExist(...)` 的报错还是通用字符串，没有带客户维度和业务语义。
