# 老庙下单接口

## 模块定位
- 入口：`LmOrderController`
- 路径前缀：`/order`
- 这不是后台业务单据模块，而是老庙渠道对 TX 系统的主动下单/改单状态通知入口。

## 接口职责
- `/order/create`
  - 老庙直采订单同步创建
  - 实际落点：`customerOrderService.createLmCustomerOrder(form)`
- `/order/stateChange`
  - 老庙订单状态同步
  - 实际落点：`customerOrderService.lmCustomerOrderStateChange(form)`
- `/order/ppNotify`
  - 老庙 PP 单生成通知
  - 实际落点：`customerOrderService.lmCustomerOrderPpNotify(form)`

## 返回口径
- 三个接口都返回 `LmApiResult`
- 成功时统一约定：
  - `status = 1`
  - `msg = 成功`
- 并按老庙协议组装：
  - `order_no`
  - `order_state`
  - `order_msg`
  - 行项目 `order_list`

## 主链特点
- 这是“渠道请求 -> 客户订单域”的接线层，不在控制器内落核心业务。
- 控制器只负责：
  - 日志打印
  - VO 校验
  - 调业务服务
  - 将异常转成老庙协议返回格式

## 权限与安全
- 三个入口都挂同一个权限：`wh:callback:lm`
- 说明系统把这组渠道接口统一看成“老庙回调/通知能力”，而不是拆成更细的订单权限。

## 关键结论
- `lmorder` 不是订单业务宿主，而是老庙订单协议适配层。
- 真正的订单创建、状态推进和 PP 通知落地都在 `customerOrderService`。
- 控制器返回里逐条回填 `order_list`，说明它不仅处理单头，也处理行级确认协议。

## 风险与疑点
- `⚠️` 三个接口都共用 `wh:callback:lm` 一套权限，粒度偏粗。
- `⚠️` 控制器里大量手工组装返回 `Map`，协议字段变更时容易漏改。
- `⚠️` 异常分支里 `status` 既有 `ServerException.code`，也有写死 `2` 的通用错误口径，失败码体系需要统一确认。
- `⚠️` 控制层只看到请求校验和日志，没看到额外签名/来源校验逻辑，需要确认是否由网关或别层承担。
