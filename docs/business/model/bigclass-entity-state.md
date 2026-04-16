# 大类档案能力图
```mermaid
flowchart TD
    A["大类建档"] --> B["校验 bigClassNo 唯一"]
    B --> C["写入 wh_big_class"]

    C --> D["修改大类"]
    D --> E["同步更新 SKU 大类编号/名称/bigType"]
    D --> F["更新计价与分类规则字段"]

    C --> G["启用大类下拉"]
    C --> H["按名称映射大类"]
    C --> I["删除大类"]

    note right of F
      salePriceMode / storePriceMode
      goodsType / goodsClassify / bigType
      都是业务规则字段
    end note
```
