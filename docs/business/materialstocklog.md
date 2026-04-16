# 原料库存日志

## 1. 模块定位

- 对应表：`wh_material_stock_log`
- 入口：`MaterialStockLogController`、`MaterialStockLogServiceImpl`
- 角色：原料库存事件表

## 2. 核心职责

- 记录原料库存每次余额/毛重变化
- 支持按业务单号、日期、网点、仓库、原料查询
- 分页/导出时补网点名、仓库名、原料名
- 支持批量插入日志

## 3. 代码结论

- `fillData(...)` 会统一回查 shop/warehouse/material 做展示补数
- `getByRequestVo(...)` 供总报表按日期和仓网料维度回查
- 它是原料库存宿主的配套事件层

## 4. 关注点

- `⚠️ deleteMaterialStockLog(...)` 是物理删除，历史追溯保护偏弱
- `⚠️ getByRequestVo(...)` 开头判空写成了判 `walletLogDOS`，明显是残留代码痕迹
