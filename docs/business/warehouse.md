# 仓库档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
仓库档案不是业务执行单，而是库存、维修、单件、来料出料、调拨、盘点等几乎所有业务链的基础主数据入口。它除了提供仓库 CRUD 外，还有两条关键规则：
1. 建仓时自动创建默认库区 `A`。
2. 仓库上维护可用材质、大类、售卖类型、部门归属等业务范围信息。

## 关键入口
- Controller: [WarehouseController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/warehouse/WarehouseController.java)
- Service: [WarehouseServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/warehouse/WarehouseServiceImpl.java)

## 真实行为
### 1. 建仓不只是插主表
`createWarehouse(...)` 会：
1. 校验 `wareNo` 唯一。
2. 校验部门存在。
3. 保存 `bigClassIds/textureIds`。
4. 插入仓库主表。
5. 自动创建默认库区：
   - `areaName = A`
   - `areaNo = A`
   - `areaType = 0`

也就是说，仓库档案和库区档案是自动串起来的。

### 2. 删仓会级联删库区
`deleteWarehouse(...)` 会：
1. 校验仓库存在。
2. 通过 `warehouseAreaService.deleteWarehouseAreaByWareId(id)` 先删该仓库下全部库区。
3. 再删除仓库主表。

### 3. 用户视角下的仓库可见范围
`getWareByUser()` 会优先取用户绑定的 `wareIds`；若用户没绑仓，则退化为“所属部门三级部门下的仓库”。这说明仓库不仅是静态主数据，也承担权限筛选维度。

## 风险与待确认
1. 删仓时虽然会级联删库区/库位，但未见“仓库已被库存、单据、单件引用时禁止删除”的保护。
2. 默认库区 `A` 是在建仓时强制自动创建的，后续很多逻辑都隐含依赖这个约定。

## 结论
仓库档案应定位为“库存域基础主数据 + 组织权限入口”，不是业务单据流。后续凡是牵涉默认仓、材质权限、大类权限、用户仓库可见范围，都会回到这个模块。
