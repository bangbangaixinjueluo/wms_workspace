# 计量单位

## 1. 模块定位

- 对应表：`wh_measure_unit`
- 入口：`MeasureUnitController`、`MeasureUnitServiceImpl`
- 角色：商品、原料、辅料等导入与展示共用的单位字典

## 2. 核心职责

- 维护计量单位主数据
- 支持 Excel 导入
- 提供按单位名、按 ID 批量回查

## 3. 代码结论

- `importExcel(...)` 会先把现有单位放进 `HashSet` 去重
- 导入时跳过空单位和重复单位
- service 仍然是薄 CRUD + 导入宿主

## 4. 关注点

- `⚠️` 删除链当前未在 service 明确暴露，但后续若补删除，需确认历史引用保护
- `⚠️ importExcel(...)` 对重复数据是静默跳过，不会回报详细失败行
