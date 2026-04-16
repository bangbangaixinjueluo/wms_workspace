# 产品规格索引

## 说明
本目录用于存放偏业务视角的规格文档，重点回答“谁在什么场景下使用什么能力、为什么这样设计、验收标准是什么”。

## 与 `docs/business` 的关系
- `docs/business/*` 是已经梳理过的业务逻辑主资料，更贴近领域对象和实际规则。
- `docs/product-specs/*` 作为二级整理层，负责把业务逻辑按“流程、地图、状态机、联调视角”重新编排，方便开发、测试和新成员阅读。
- 若两者出现冲突，应优先回到 `docs/business/*` 校正。

## 当前文档
| 文档 | 状态 | 说明 |
| --- | --- | --- |
| [new-user-onboarding.md](./new-user-onboarding.md) | aligned | 系统首次使用与联调准备说明 |
| [wms-core-flow.md](./wms-core-flow.md) | draft | WMS 主业务流概览 |
| [inventory-lifecycle.md](./inventory-lifecycle.md) | draft | 库存对象与库存变化生命周期 |
| [role-and-permission-matrix.md](./role-and-permission-matrix.md) | draft | 菜单、按钮、接口、数据范围权限说明 |
| [wms-domain-map.md](./wms-domain-map.md) | draft | 按目录组织的 WMS 业务域地图 |
| [bill-status-machine-overview.md](./bill-status-machine-overview.md) | draft | 核心单据状态流转导航 |
| [goods-inbound-bill-status.md](./goods-inbound-bill-status.md) | draft | 成品入库单状态流转 |
| [transfer-bill-status.md](./transfer-bill-status.md) | draft | 调拨单状态流转 |
| [sale-bill-status.md](./sale-bill-status.md) | draft | 销售结算单状态流转 |
| [return-bill-status.md](./return-bill-status.md) | draft | 客户退货单状态流转 |
| [counting-bill-status.md](./counting-bill-status.md) | draft | 盘点单状态流转 |

## 建议补充
- 库存生命周期
- 单据状态流转
- 角色与权限矩阵
- 订单与财务联动口径
