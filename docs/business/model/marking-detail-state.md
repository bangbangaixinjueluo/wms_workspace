# 字印单明细能力图
```mermaid
flowchart TD
    A["wh_marking_bill"] --> B["wh_marking_detail"]
    B --> C["getPage 联表视图"]
    C --> D["客户/业务员/字印内容/SKU 图片"]
    A --> E["marked/obsolete"]
    E --> B
```
