# 材质档案

## 模块定位
- 入口：`MaterialQualityController`、`MaterialQualityServiceImpl`
- 宿主表：`wh_material_quality`
- 这是原料品质/材质品质规则主数据。

## 关键入口
- `/wh/material-quality/create`
- `/wh/material-quality/update`
- `/wh/material-quality/page`
- `getMaterialQualityByQualityNo(...)`
- `getMaterialQualityByBigClassNo(...)`
- `getMaterialQualityByRememberNo(...)`
- `getMaterialQualityByWalletTypeNo(...)`

## 真实行为
- 提供基础 CRUD、分页和导出。
- 除了主键查询，还提供多套唯一口径反查：
  - `qualityNo`
  - `bigClassNo`
  - `rememberNo`
  - `walletTypeNo`
- 说明这张表不是普通字典，而是会被多条业务链按不同编码体系引用。

## 在主链中的作用
- 给原料品质、材质记忆码、钱包类型映射等场景提供统一主数据。
- 作为原料/钱包/大类之间的一层规则桥接。

## 关键结论
- 这层更像“原料品质规则主档”，而不是简单下拉字典。
- `walletTypeNo` 已经把它接进了资金口径。

## 风险与待确认
- `⚠️` `deleteMaterialQuality(...)` 仍是物理删除，未见对原料、钱包和历史业务引用的保护。
- `⚠️` 当前只看到存在性校验，没看到 `qualityNo/bigClassNo/rememberNo/walletTypeNo` 的显式唯一性保护逻辑，是否完全依赖数据库约束需要确认。
