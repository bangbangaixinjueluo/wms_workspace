# 前端乱码维护说明

## 适用场景

`wms-web` 内部分历史 Vue 文件存在编码不稳定现象。直接在模板或脚本里写中文，可能在保存、合并或不同编辑器打开后出现乱码。

本说明用于约束后续在这些页面上追加中文文案时的安全写法，避免再次出现“按钮文字正常提交后变乱码”的问题。

## 推荐写法

### 1. 模板里的固定中文文案

优先使用 HTML 实体。

示例：

```vue
<el-button>&#23436;&#32467;</el-button>
```

### 2. JS 中的提示语、成功提示

优先使用 Unicode 转义。

示例：

```js
this.$modal.confirm('\u662f\u5426\u5b8c\u7ed3\u5355\u53f7\u4e3a"' + orderNo + '"\u7684\u5355\u636e?')
this.$modal.msgSuccess("\u5b8c\u7ed3\u6210\u529f")
```

## 本次已验证页面

- `wms-web/src/views/wh/order/demandOrder/index.vue`
- `wms-web/src/views/wh/order/demandOrder/detail.vue`
- `wms-web/src/views/wh/order/demandOrderContainer/index.vue`
- `wms-web/src/views/wh/order/demandOrderContainer/detail.vue`

## 维护建议

- 做需求前，若页面存在历史乱码，先检查该文件是否为稳定 UTF-8。
- 无法确认时，不要继续直接写中文字面量。
- 优先使用实体或 Unicode 转义，保证展示结果与文件编码解耦。
