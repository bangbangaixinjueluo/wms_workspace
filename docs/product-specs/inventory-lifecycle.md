# 库存生命周期

## 目的
本文用于从产品与联调视角总结 WMS 中“哪些动作会影响库存、影响到哪一层库存、该去哪里查主规则”。

更完整的业务事实和状态关系，优先参考：
- [docs/business/overview.md](/G:/wms_workspace/docs/business/overview.md)
- [docs/business/state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)
- [docs/business/goodsinboundbill.md](/G:/wms_workspace/docs/business/goodsinboundbill.md)
- [docs/business/transferbill.md](/G:/wms_workspace/docs/business/transferbill.md)
- [docs/business/salebill.md](/G:/wms_workspace/docs/business/salebill.md)
- [docs/business/returnbill.md](/G:/wms_workspace/docs/business/returnbill.md)
- [docs/business/iteminventorybill.md](/G:/wms_workspace/docs/business/iteminventorybill.md)

## 库存对象分层
当前系统里的“库存”不是单一概念，而是按业务对象分层管理：

- 成品或货品库存：`goodsStock / goodsStockLog`
- 单件库存：`item / historyStock / inventoryBill`
- SKU 维度库存：`skuStock / skuStockDaily`
- 物料库存：`materialStock / materialStockLog`
- 配件库存：`accessoryStock / accessoryStockLog`
- 共享、寄存、客留等衍生库存口径

## 生命周期主线
### 1. 主数据准备
库存动作发生前，必须先具备商品、SKU、客户、供应商、仓库、库区、库位等基础主数据。

### 2. 入库形成
典型入口包括：

- 成品入库
- 单件入库
- 物料来料
- 配件入库
- 维修回流或委外回流

### 3. 在库流转
典型动作包括：

- 调拨
- 库位调整
- 盘点
- 共享、寄存、客留等中间态流转

### 4. 出库与消耗
典型动作包括：

- 销售出库结算
- 发货
- 原料出料
- 损耗
- 维修外发

### 5. 差异修正
典型动作包括：

- 盘点差异处理
- 盈亏调整
- 反审、作废、重做类回滚

## 理解边界
这条生命周期是导航图，不是每张单据的精确状态机。

- 要看“入库后改什么”，先看对应业务单据文档。
- 要看“调拨和接收是不是两步”，看调拨状态链。
- 要看“发货完成究竟发生在哪一步”，看出库结算单文档。
- 要看“盘点审核是否真的影响库存”，看单件盘点单文档。

## 联调建议
- 至少分别验证成品、单件、物料三条库存链。
- 对每条链都验证正向动作和反向回滚动作。
- 同时核对库存主表、库存日志和报表口径是否一致。

## 与业务主资料的关系
本文只负责库存主题导航。

- 如果要确认库存真实过账点和回滚点，优先回到 `docs/business/*` 的单据级文档。
- 如果要确认它在整个系统状态体系里的位置，优先看 [state-matrix.md](/G:/wms_workspace/docs/business/state-matrix.md)。
