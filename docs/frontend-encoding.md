# 前端乱码维护说明

## 适用场景

`wms-web` 内部分历史 Vue 文件可能因为编辑器、终端代码页或旧提交方式，出现“源码看起来乱码”或“保存后中文异常”的情况。

本说明用于约束后续在这些页面上追加中文文案时的处理顺序，既保证页面不乱码，也尽量保证源码可读。

## 默认原则

默认优先保留中文直写。

示例：

```vue
<el-button>完结</el-button>
```

```js
this.$modal.confirm('是否完结单号为"' + orderNo + '"的单据?')
this.$modal.msgSuccess("完结成功")
```

## 处理顺序

1. 先确认文件能按 UTF-8 正常读取和保存。
2. 若 UTF-8 正常，则直接使用中文，不要为了规避风险主动改成实体或 Unicode 转义。
3. 只有在某个历史文件中，中文直写会反复被保存成乱码时，才临时改用 HTML 实体或 Unicode 转义兜底。

## 本次已验证页面

- `wms-web/src/views/wh/order/demandOrder/index.vue`
- `wms-web/src/views/wh/order/demandOrder/detail.vue`
- `wms-web/src/views/wh/order/demandOrderContainer/index.vue`
- `wms-web/src/views/wh/order/demandOrderContainer/detail.vue`

以上页面已确认可以恢复为中文直写。

## 维护建议

- 做需求前，若页面有历史乱码痕迹，先判断是文件编码问题还是终端显示问题。
- 如果只是终端显示异常，不要误把正常中文源码替换成难读的转义内容。
- 只有在确认文件保存链路会破坏中文时，才使用转义方案作为例外处理。
