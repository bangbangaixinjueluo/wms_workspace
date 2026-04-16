# 业务分区部门映射

## 模块定位
- 入口：`BusinessAreaDeptServiceImpl`
- 宿主表：`wh_business_area_dept`
- 这是业务分区和部门之间的映射卫星层。

## 关键入口
- `getBusinessAreaDeptListByAreaId(...)`
- `getBusinessAreaDeptListByAreaIds(...)`

## 真实行为
- 提供基础 CRUD。
- 核心查询口径围绕 `areaId -> dept` 批量映射展开。
- service 很薄，说明真实消费语义在业务分区和组织权限链外层。

## 在主链中的作用
- 给业务分区维度补挂部门。
- 支撑后续按分区联动组织或权限的外层逻辑。

## 风险与待确认
- `⚠️` 删除仍是物理删除。
- `⚠️` 该层本身没有状态和引用保护，若上层把它当“稳定主数据”使用，历史关系会漂移。
