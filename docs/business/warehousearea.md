# 库区档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
库区档案是仓库下一级基础主数据，真实上同时承担“库区管理 + 默认库位生成”的职责。它不是业务单据，也没有审核流。

## 关键入口
- Controller: [WarehouseAreaController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/warehousearea/WarehouseAreaController.java)
- Service: [WarehouseAreaServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/warehousearea/WarehouseAreaServiceImpl.java)

## 真实行为
### 1. 建库区会自动建默认库位
`createWarehouseArea(...)` 会：
1. 校验所属仓库存在。
2. 校验同仓 `areaNo` 不重复。
3. 保存库区，且 `areaName` 被强制写成 `areaNo`。
4. 自动创建默认库位：
   - `localNo = areaNo + 0`
   - `localName = areaNo + 0`
   - `localType = TEMPORARY`

所以库区不是纯主档，它还负责默认库位初始化。

### 2. 默认库区不能删
`deleteWarehouseArea(...)` 明确禁止删除：
1. `areaName = A`

删除普通库区时，会先删该库区下全部库位，再删库区。

### 3. 默认库区获取有自动补洞逻辑
`getDefaultArea(wareId)` 如果某仓没有任何库区，会直接补建：
1. `areaName = A`
2. `areaNo = A`

然后返回该默认库区。说明业务层对“仓库一定存在默认 A 库区”有强假设。

## 风险与待确认
1. `getDefaultArea(wareId)` 在补建默认库区时没有显式回填 `wareId/deptId`，需要确认这个兜底分支是否真的会跑到。
2. 默认库区判断既看“名称包含默认”也看“包含 A”，规则偏松，可能误命中普通命名。

## 结论
库区档案应定位为“仓库结构层主数据 + 默认库位生成入口”。后续任何依赖默认库区的建单、库存、单件、维修、盘点逻辑，都要把这个模块一起看。
