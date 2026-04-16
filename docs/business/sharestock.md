# 共享库存

## 模块定位
- 对应主表：`wh_share_stock`
- 配套表：`wh_share_flow_record`、`wh_lm_api_transfer`、`wh_share_surplus_day_report`
- 入口：`ShareStockController`、`ShareStockServiceImpl`、`ShareStockBusiness`
- 这不是单纯库存报表，而是“共享库存主表 + 老庙接口流水 + API 痕迹 + 差异调平 + 结余日报”的复合执行链。

## 主数据职责
- `wh_share_stock` 维护共享库存主快照，核心维度是：
  - `customerId`
  - `skuId / skuNo`
  - `modelNo`
  - `specs`
- 数量口径分成四组：
  - `shareStock`：累计可共享库存
  - `surplusStock`：当前结余库存
  - `lockStock`：客户锁定库存
  - `diffStock`：待调平差异量

## 开启/关闭共享
- `onShareStock(customerId, localIds)`：
  - 先按客户加 Redisson 锁
  - 只对配置里的老庙客户生效
  - 按库位查 `skuStock`
  - 与现有共享库存按 `skuNo:modelNo:specs` 合并
  - 生成批量流水 `wh_share_flow_record`
  - 调老庙 `batchPushInOutOrder(...)`
  - 成功后新增或更新 `wh_share_stock`
- `offShareStock(customerId, localIds)`：
  - 同样只对老庙客户生效
  - 生成负数出库流水
  - 调老庙批量接口
  - 成功后清空该客户的共享库存记录

## 流水同步链
- `handleSync(flowRecordId)` 是共享库存消息处理主链：
  - 对单条流水加锁
  - 判断是否属于老庙订单来源
  - 调老庙单笔 `pushInOutOrder(...)`
  - 写 `wh_lm_api_transfer`
  - 成功则累计回写 `wh_share_stock`
  - 失败则记录失败原因
- `flowRecordRetry(recordId)` 支持失败流水重试，但显式排除了“调整结余库存”类型。
- `flowRecordDiscard(recordId)` 不是删流水，而是把流水 `enable` 改成禁用。

## 差异调平
- `adjustSurplusStock(shareId, quantity)`：
  - 把 `diffStock` 写入主表
  - 立即发起老庙单笔调平接口
- `stockAdjustAll()`：
  - 批量处理所有 `diffStock != 0` 的共享库存
- 调平成功后：
  - `clearDiffStock(id)`
  - 用 `surplus_stock = surplus_stock + diff_stock`
  - 再把 `diffStock` 清零

## 查询与运维
- 共享库存分页支持按：
  - `customerId`
  - `skuNo`
  - `modelNo`
  - `specs`
  - `diffTag`
- 流水分页会补：
  - `skuName`
  - 老庙接口响应报文
  - API 痕迹对象
- `getItemInfo(modelNo, specs)` 可直接查看老庙客户共享库位下的单件明细。
- `compareLmStock(file)` 可导入 Excel 对比老庙侧共享库存差异。

## 日结与报表
- `saveSurplusReport(...)` 把共享结余落到 `wh_share_surplus_day_report`。
- `clearSurplusReportByDay(reportDate)` 支持按日报日期清理旧备份。
- `shareAllStockReport` 会调用老庙“昨日结余核对”接口，并记录 API 痕迹。

## 关联模块
- `skustock`：共享开关和库存推送的直接库存来源。
- `skumodel`：共享库存维度里直接使用 `modelNo`。
- `customerlocation`：决定哪些客户库位参与共享。
- `item`：单件级共享明细查看来源。
- `transferbill / pack / salebill / customerorder`：用于判断流水是否属于老庙订单来源。
- `shareparty`：共享参与方与库位配置。

## 关键结论
- 共享库存真正的业务对象不是一张表，而是“主表 + 流水 + API 痕迹 + 日报备份”的组合。
- 这条链已经深度耦合老庙接口，不是内部库存统计。
- `modelNo + specs` 已经成为共享库存和老庙对账的重要维度，不只是 SKU 附属字段。

## 风险与疑点
- `⚠️` `offShareStock(...)` 成功后是按 `customerId` 全量删除共享库存，不是只删本次 `localIds` 对应的维度，影响范围偏大。
- `⚠️` `onShareStock(...)` / `offShareStock(...)` 捕获异常后只打日志不抛出，外部调用方可能误以为操作成功。
- `⚠️` `flowRecordRetry(...)` 禁止重试“调整结余库存”流水，意味着调平失败后只能重新发起新调平，不能复用原流水。
- `⚠️` 差异调平成功依赖 `clearDiffStock()` 的 SQL 语句做库存回补，若并发修改 `diffStock`，结果容易漂移。
