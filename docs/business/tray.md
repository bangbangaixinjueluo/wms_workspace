# 小包

## 模块定位
- 对应表：`wh_tray`
- 入口：`TrayController`、`TrayServiceImpl`
- 这不是独立单据，而是产品包里的“小包宿主层”，承接 `packNo/trayNo/trayOrder` 这组包内分组语义。

## 主数据职责
- 关键字段包括：
  - `packId/packNo`
  - `billId`
  - `trayOrder`
  - `trayNo`
  - `trayName`
  - 各类合计重量、工费、复核毛重字段
- `trayNo` 的真实业务语义是“产品包号 + 包内盘次号”，不是全局独立业务单号。

## 主要业务动作
- `createTray(...)`
  - 只做小包主表保存。
  - 创建人名称由 `CommonService` 回填。
- `updateTray(...)`
  - 修改小包单头。
- `updateTrayDetail(...)`
  - 真正高频用的是明细批量更新入口，不是改单头。
- `delete/deleteAll/deleteByBillidOrPackid`
  - 这些入口都会联动删除 `wh_tray_detail`。
  - 但删除动作主要在控制器里编排，不是服务层统一封装。

## 查询与展示口径
- `getTray(id)`
  - 不是纯读 `wh_tray`。
  - 会继续拉 `wh_tray_detail`，再逐条反查 `wh_item` 补：
    - `weight`
    - `grossWeight`
    - `tareWeight`
    - `goldWeight`
    - `skuNo`
    - 图片和名称
  - 同时重新汇总净重、皮重。
- `getTrayListByPackNo(packNo)`
  - 会把配置项 `SMALL_BAG_STICKER_KEY` 读取出来。
  - 用它计算 `totalGrossWeightPackage = totalGrossWeightRepeat + 不干胶重`。
- 所以前端看到的小包，不只是主表快照，而是“主表 + 明细 + 单件 + 系统配置”的拼装视图。

## 上游生成链
- `PackBusiness`
  - 产品包建单、改单时会重建小包。
  - 审核时还会校验小包 `totalGrossWeightRepeat >= totalGrossWeight`。
- `CustomerOrderIntentBusiness`
  - 现场配包也会自动生成 `trayOrder/trayNo`。

## 下游使用链
- `salebill`
  - 销售结算会按 `trayNo` 取产品包明细。
- `pack`
  - 产品包导出、详情页、老庙模板导出都会继续按小包维度组织展示。
- `traydetail`
  - 小包明细是小包真实内容宿主，`tray` 自身只是包内头表。

## 关键结论
- 小包不是独立业务单，而是产品包执行域下的卫星宿主。
- 真正重要的不是 CRUD，而是它承接了“包内分组、复核毛重、不干胶重量、按小包销售/导出”这些业务口径。
- 任何改动都不能只看 `wh_tray` 主表，必须连 `wh_tray_detail`、`wh_item` 和配置项一起看。

## 风险与疑点
- `⚠️` 删除链主要写在控制器里循环删主表和明细，未见统一事务化封装与状态保护。
- `⚠️` `getTray(...)` 展示重量更多依赖实时反查 `wh_item`，不完全等于小包建单时快照。
- `⚠️` `getTrayListByPackNo(...)` 里的 `totalGrossWeightPackage` 强依赖系统配置 `SMALL_BAG_STICKER_KEY`，配置异常会直接影响展示口径。
