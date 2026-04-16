# 设计元素
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
设计元素不是普通字典，而是 SKU、新建款、改款、企微详情、导入分析共同依赖的规则主数据。它同时承载：
- 元素编码 `designNo`
- 元素名称 `designName`
- 元素大类 `elementBigType`
- 元素子类 `elementSubType`

并和品类、字典表一起组成设计元素 1/2/3/4 的校验口径。

## 关键入口
- Controller: [DesignElementsController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/designelements/DesignElementsController.java)
- Service: [DesignElementsServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/designelements/impl/DesignElementsServiceImpl.java)
- Entity: [DesignElements.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/designelements/DesignElements.java)

## 真实行为
### 1. 设计元素编码不是手填，而是系统生成
`create(...)` 会先：
1. 固定前缀 `YS`
2. 取当前最大编码尾号
3. 左补零到 5 位
4. 生成 `designNo`

所以设计元素编码是系统流水号，不是业务手工主键。

### 2. 唯一性不是全局名称唯一，而是“元素大类 + 子类 + 名称”唯一
`checkUnique(...)` 校验的是：
- `elementBigType`
- `elementSubType`
- `designName`

这说明相同名称如果挂在不同大类或子类下，业务上可能是允许共存的。

### 3. 元素子类不是单一字典来源
分页和选项逻辑显示，设计元素子类分为多套来源：
- 大类 `1`
  - 子类来自品类名称
- 大类 `2`
  - 子类来自字典 `design_element2`
- 大类 `3`
  - 子类来自字典 `design_element3`
- 大类 `4`
  - 子类来自字典 `design_element4`

也就是说，设计元素不是单表自闭环，而是“品类 + 字典”混合驱动的规则主数据。

### 4. 设计元素 1 有特殊业务口径
`all(...)` 里对 `elementBigType = 1` 做了特殊判断：
- 除手镯、戒指、挂坠、手链、手足链等少数品类外
- 其余情况优先取 `elementSubType = ""` 的数据

这说明设计元素 1 不是简单按品类全量匹配，而是带一层“通用元素 vs 指定品类元素”的业务规则。

### 5. 这张表是 SKU 审批域设计元素校验源
`getDOMapByNos(...)`、`getMapByNames(...)`、`getMapByBigType(...)` 都在 SKU 审批域里高频被使用：
- 新建款导入分析
- 改款设计元素修改
- 设计元素批量修改
- 企微详情显示

特别是改款批量设计元素修改时，还会结合：
- 品类归属
- 设计元素 2/3/4 字典映射

一起校验，所以它是规则源，不只是展示数据。

## 风险与待确认
1. `designNo` 固定用 `YS + 5 位流水号`，需要确认后续数量增长后是否仍满足编码长度约束。
2. 元素子类来源同时依赖品类和三套字典，任何一边改名都可能直接影响新建款/改款校验链。
3. 设计元素 1 的“部分品类走通用空子类”规则是硬编码名单，后续新增品类时很容易漏补。
4. 当前只看到创建、更新、分页、选项、映射能力，没看到删除保护；需确认是否允许被 SKU 历史引用的元素继续被修改或停用。

## 结论
设计元素应定位为“SKU 设计语义规则主数据”。它不只是字典，而是新建款、改款、导入、批量设计元素修改共同依赖的校验与映射源。
