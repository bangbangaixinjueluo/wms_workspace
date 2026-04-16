# 客户共享库位

## 模块定位
- 对应表：`wh_customer_location`
- 入口：`CustomerLocationController`、`CustomerLocationServiceImpl`、`CustomerLocalBusiness`
- 这不是普通客户资料子表，而是“客户共享库位配置 + 共享库存开关入口”宿主。

## 主数据职责
- 关键字段包括：
  - `customerId`
  - `wareId`
  - `localId`
- 这张表本身很轻，但它决定了“哪些客户、哪些库位参与共享库存”。

## 主要业务动作
- 基础 CRUD 由 `CustomerLocationServiceImpl` 提供。
- `/page` 实际不是只查 `wh_customer_location`，而是：
  - 先查仓库下全部库位
  - 再按 `customerId + localId` 判断该库位是否已共享
  - 返回“已共享/未共享”的混合视图
- 所以这个分页页本质是“客户共享视图”，不是单表分页。

## 共享开关链
- `/shareAll`
  - 先取客户在指定仓库下的当前共享库位
  - 批量删旧绑定
  - 调 `shareStockService.offShareStock(...)`
  - 再按新库位批量建 `wh_customer_location`
  - 调 `shareStockService.onShareStock(...)`
- `/shareOne`
  - 单个库位开启/关闭共享
  - 也是“先改绑定，再推共享库存”
- 说明 `wh_customer_location` 改动不是静态主数据修改，而是会直接触发共享库存链动作。

## 查询补数
- `fillData(...)` 会补：
  - 仓库名/仓库编号
  - 库位名/库位编号/库位类型
  - 客户名/客户编号
- 这也说明前端看到的是“客户 + 库位”的配置视图，不是纯关系表。

## 关联模块
- `customer`：共享归属客户。
- `warehouselocation`：共享对象库位。
- `sharestock`：配置变化后真正被推送的共享库存主链。
- `shareparty`：共享方规则宿主，与客户共享库位一起构成上游共享范围。
- `customerorder`：订单域查询共享范围时会直接取客户共享库位。

## 关键结论
- 客户共享库位不是普通关系表，而是共享库存域的上游配置入口。
- 真正的业务意义不在 `wh_customer_location` 自身，而在于它一变更就会触发共享库存开关、关闭和重推。

## 风险与疑点
- `⚠️` `shareAll(...)` 当前是“先删旧绑定，再 offShareStock，再建新绑定，再 onShareStock”，中途失败时容易出现配置和共享库存不一致。
- `⚠️` `getCustomerLocationVOSharePage(...)` 返回的 `total` 直接沿用仓库全部库位总数，不一定等于按共享状态过滤后的列表条数。
- `⚠️` 删除当前是物理删除，未见“已被共享库存/订单引用时”的保护。
