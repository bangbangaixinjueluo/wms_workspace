# 材质档案能力图
```mermaid
flowchart TD
    A["材质建档"] --> B["校验名称唯一"]
    A --> C["校验编码唯一"]
    A --> D["校验编码为数字"]
    D --> E["写入 wh_texture"]

    E --> F["修改材质"]
    F --> G["同步更新 SKU 材质编号/名称"]
    F --> H["更新钱包类型映射语义"]
    F --> I["更新半成品分类语义"]

    E --> J["启用材质下拉"]
    E --> K["按材质取钱包类型映射"]
    E --> L["删除材质"]

    note right of K
      walletTypeNo / walletGoodsType
      是业务强语义字段
    end note
```
