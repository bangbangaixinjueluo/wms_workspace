# WMS 业务域地图

## 目的
本文按当前前后端目录和业务文档，把 WMS 的主要业务域做成一张导航地图，方便定位模块与阅读入口。

更完整的模块清单和状态矩阵，以以下文档为准：
- [docs/business/overview.md](/G:/wms_workspace/docs/business/overview.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)

## 业务域总览
### 主数据与规则域
- `basic`
- `merchant`
- `product`

负责客户、供应商、SKU、材质、仓库、库位、业务员等基础主数据。

### 库存执行域
- `goods`
- `item`
- `material`
- `accessory`
- `counting`

负责入库、出库、调拨、盘点、共享、库存日志等核心库存动作。

### 销售与订单域
- `sale`
- `order`
- `customerRetain`
- `exhibition`

负责订单、产品包、出库结算、退货、客留存和展厅相关链路。

### 财务结算域
- `finance`
- `wallet`
- `shopBank`
- `settlement`

负责收付款、应收应付、结价、调账、钱包和银行账。

### 设计与款式域
- `style`
- `styleDesign`
- `styleUpdate`
- `styleDownOrUp`
- `design`

说明这个项目不只是仓储系统，也承载了较强的款式与设计业务。

### 报表与任务域
- `stockReport`
- `stockReportDetail`
- `saleReport*`
- `syncTask`
- `mytask`

负责统计报表、同步任务、外围协同与部分异步能力。

## 前后端对齐关系
当前代码里，`wms-web/src/views/wh` 与 `wms-web/src/api/wh` 大体是一一对应的。

这意味着：
- 看页面归属，可以快速回到 API 目录。
- 看 API 归属，也能较快找到对应页面。
- 做文档补齐时，适合按“业务域 -> 页面/API -> 后端控制器 -> 服务链”这条路径继续展开。

## 后端主归属
后端业务主要集中在 `wms-api/yudao-module-wh`，并通过控制器、业务层、服务层和 mapper 组合实现。

更具体的分层原则，以 [docs/CODE_RULES.md](/G:/wms_workspace/docs/CODE_RULES.md) 为准。

## 与业务主资料的关系
本文只负责地图导航，不定义最终业务规则。

- 想看全量模块职责，请回到 [overview.md](/G:/wms_workspace/docs/business/overview.md)。
- 想看状态并发与主状态来源，请回到 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
