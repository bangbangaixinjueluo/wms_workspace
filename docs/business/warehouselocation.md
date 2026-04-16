# 库位档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
库位档案是仓库结构最细粒度的基础主数据。它不仅提供库位 CRUD，还承担：
1. 默认库位兜底。
2. 共享库位开关。
3. 各种业务单据里“默认库位/当前库位”的来源。

## 关键入口
- Controller: [WarehouseLocationController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/warehouselocation/WarehouseLocationController.java)
- Service: [WarehouseLocationServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/warehouselocation/WarehouseLocationServiceImpl.java)
- DO: [WarehouseLocationDO.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/dal/dataobject/warehouselocation/WarehouseLocationDO.java)

## 真实行为
### 1. 建库位会把名称强制对齐编号
`createWarehouseLocation(...)` 与 `updateWarehouseLocation(...)` 都会把：
1. `localName = localNo`

所以当前库位名称并不是自由文本主数据，而是基本等同于编号。

### 2. 默认库位不能删
`deleteWarehouseLocation(...)` 明确禁止删除：
1. `localNo = A0`

但 `getDefaultLocal(wareId)` 的自动补洞又写成：
1. `localNo = A01`
2. `localName = A01`

而建库区时自动生成的默认库位是：
1. `localNo = areaNo + 0`
2. 默认库区 A 下即 `A0`

这说明当前代码里至少存在 `A0` 和 `A01` 两套“默认库位”口径。

### 3. 默认库位获取带兜底建档
`getDefaultLocal(wareId)` 若仓库下没有任何库位，会：
1. 先通过 `getDefaultArea(wareId)` 取默认库区。
2. 插入 `A01` 默认库位。
3. 但方法最后 `return null`。

这意味着兜底建档分支建完默认库位后，本次调用拿不到刚建的结果。

### 4. 共享库位
`shared(ids, stockShare)` 会：
1. 只允许启用状态库位切换共享。
2. 关闭共享时删除 `share_party_local` 关联。
3. 批量更新 `stockShare`。

说明库位还是共享库存模块的主数据入口。

## 风险与待确认
1. 默认库位口径不一致：建库区自动生成的是 `A0`，`getDefaultLocal(...)` 兜底补的是 `A01`，删除保护拦的是 `A0`。
2. `getDefaultLocal(...)` 在无库位时插入了 `A01` 但直接 `return null`，调用方本次拿不到默认库位对象。
3. `getWarehouseLocationVOList(...)` 里 `if(respList == null)` 才回填数据，条件看起来写反了，正常情况下不会执行回填。
4. 删库位未见“已有单件、库存、库位共享关系引用时禁止删除”的保护。

## 结论
库位档案应定位为“仓储结构最细粒度主数据 + 默认库位/共享库位入口”。它不是执行单，但很多执行单的默认仓位、移动仓位、共享仓位都依赖它；当前最需要关注的是 `A0/A01` 默认库位口径不一致的问题。
