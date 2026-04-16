# 老庙客户款式库

## 模块定位
- 对应表：`wh_sku_customer_lm`
- 入口：`SkuCustomerLmController`、`SkuCustomerLmServiceImpl`
- 这不是普通客户款式关系，而是面向“老庙”渠道的外部商品映射层。

## 主数据职责
- 以正式 `sku` 为源，一次性生成老庙所需的外部商品字段集合。
- 建档时会从正式 SKU 自动投影：
  - `skuUuid/outSkuNo/customerSkuNo`
  - `customerSkuName`
  - `minGramWeight/maxGramWeight`
  - `deliveryCycle`
  - `goodsName`
  - `partsDesc`
  - `skuRemark`
- 还会把品牌固定写成 `老庙`，最小起订量固定写成 `1`。

## 老庙枚举映射
- 创建时会把正式 SKU 字段翻译成老庙口径枚举：
  - `bigClassName -> LmProductTypeEnum -> productType`
  - `textureName -> LmTextureEnum -> goldFineness`
  - `categoryName -> LmCategoryEnum -> topClass`
  - `varietyTechnics -> LmVarietyTechnicsEnum -> processClass`
  - `productName -> LmProductNameEnum -> fiveClass/skuClass`

## 主流程
- 支持一批 `skuIds` 批量挂到同一个客户下。
- 每个 SKU 创建前会校验：
  - 正式 SKU 存在
  - 同一客户下该 SKU 尚未建立老庙映射
- 更新是单行直接改。
- 删除是物理删除。

## 查询与回显
- 分页、导出围绕老庙字段体系展开，支持按：
  - 外部款号
  - 商品名称
  - 品牌
  - 产品类型
  - 大类/工艺/款式分类
  - 五级分类
  - 唯一编号
  - 成色
  - 财务分类
  - 宝石/辅石材质
- 详情回显时继续补客户名称、正式 SKU 编码/名称和正式 SKU 图片。

## 关键字段
- `outSkuNo`：老庙外部款号。
- `skuUuid`：唯一编号，当前创建时直接复用正式 `skuNo`。
- `productType/topClass/processClass/skuClass/fiveClass`：老庙商品分类体系。
- `goldFineness`：老庙成色口径。
- `brand`：当前固定为 `老庙`。

## 关键结论
- 这层本质是“正式 SKU -> 老庙外部商品字段模型”的投影缓存层。
- 它和 `customersku`、`skucustomer` 最大区别在于：不是维护客户本地款号，而是维护渠道专用外部商品口径。

## 风险与疑点
- `⚠️` 绝大多数字段都在创建时从正式 SKU 一次性复制，正式 SKU 后续改名、改分类、改工艺后这里不会自动回刷。
- `⚠️` `skuUuid/outSkuNo/customerSkuNo` 当前都直接取正式 `skuNo`，三者并没有真正分离。
- `⚠️` 枚举映射大量依赖 `Lm*Enum.getEnumByText(...)` 文本匹配，正式主数据改名会直接影响建档成功率和映射结果。
- `⚠️` 删除仍是物理删除，没看到对老庙下游接口或同步链的引用保护。
