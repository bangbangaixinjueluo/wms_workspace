# 材质档案
> 基于 commit: `48af575a1314636c88e9f05ca3cb4443f88865bd`，日期：2026-03-31

## 模块职责
材质档案不是业务执行单，而是库存、来料出料、钱包、SKU、半成品分类等链路共用的关键主数据。它除了维护材质基础属性外，还承担“材质 -> 钱包类型”的映射，以及材质名称/编码变更时对 SKU 冗余字段的同步。

## 关键入口
- Controller: [TextureController.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/controller/admin/texture/TextureController.java)
- Service: [TextureServiceImpl.java](/D:/ws/code/wms-api/yudao-module-wh/yudao-module-wh-biz/src/main/java/cn/iocoder/yudao/module/wh/service/texture/TextureServiceImpl.java)

## 真实行为
### 1. 建档会校验“名称唯一 + 编码唯一 + 编码必须是数字”
`createTexture(...)` 会校验：
1. `textureName` 唯一。
2. `textureNo` 唯一。
3. `textureNo` 必须满足数字格式。

通过后直接写入 `wh_texture`，没有审批流，也没有额外缓存态。

### 2. 修改材质会同步改 SKU 冗余材质信息
`updateTexture(...)` 除了更新材质主表，还会先执行：
- `skuMapper.updateTexture(id, textureNo, textureName)`

也就是说，SKU 里冗余保存了材质编号和材质名称，材质改名/改编码不是单表改动，而是“材质主档 + SKU 冗余字段”一起更新。

### 3. 删除是裸删，未见引用保护
`deleteTexture(id)` 只做两步：
1. 校验材质存在。
2. `textureMapper.deleteById(id)` 物理删除。

没看到对 SKU、库存、钱包、来料出料、单件等历史引用的阻断。

### 4. 材质和钱包类型是强语义映射
`TextureDO` 里直接保存：
- `walletTypeNo`
- `walletTypeName`
- `walletGoodsType`
- `convertRate`

`getTextureIdWalletMapByIds(...)` 还会把材质 ID 批量映射成 `walletTypeNo`，并在缺材质时直接抛错。说明钱包材质账并不是自由文本，而是依赖材质档案里的稳定映射。

### 5. 材质还承担半成品归类语义
`TextureDO` 还带：
- `semisClassNo`
- `semisClassName`
- `bigClassId`

控制器里也开放了 `semisClassOptions`。这说明材质不仅服务原料钱包口径，也参与半成品大类归属和一些生产分类筛选。

### 6. 启停是简单主档状态，不带审批语义
材质列表下拉默认只取 `status = 0` 的启用材质；但当前代码里没有独立“状态切换接口”，更多是创建/修改时直接维护主档状态字段。

## 关键实体
- `wh_texture`：材质主档。
- `wh_sku`：材质编号/名称冗余回写目标。
- 钱包类型映射：通过 `walletTypeNo/walletGoodsType` 参与材质钱包口径。
- 半成品分类：通过 `semisClassNo/semisClassName/bigClassId` 参与分类归属。

## 风险与待确认
1. `deleteTexture(...)` 是物理删除，没看到对 SKU、库存、来料出料、钱包流水等历史引用的保护。
2. `updateTexture(...)` 只同步更新了 SKU 冗余材质字段，没看到同步刷新材质钱包类型映射下游缓存或其他历史业务冗余。
3. `isNumeric(...)` 允许小数和负号，但报错文案写的是“编号是数字类型”，需要确认材质编码是否真的允许负数/小数。
4. `getByNamesMap(...)` 里对 `deleted` 的筛选写成了 `eq(TextureDO::getDeleted, CommonStatusEnum.ENABLE.getStatus())`，需要确认这里的“启用状态值”和“未删除标志值”是否恰好一致。

## 结论
材质档案应定位为“库存与钱包共用的高频口径主数据”。它不是单纯字典，因为材质一旦变化，会继续影响 SKU 冗余字段、材质钱包映射、半成品分类和多个业务单据的材质识别口径。
