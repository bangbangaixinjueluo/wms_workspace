# 仓库结构能力图

```mermaid
flowchart TD
    A["仓库档案"] --> B["自动创建默认库区 A"]
    B --> C["库区档案"]
    C --> D["自动创建默认库位 A0"]
    D --> E["库位档案"]

    E --> F["默认库位获取 getDefaultLocal"]
    E --> G["共享库位开关 stockShare"]

    note right of A
      全部属于基础主数据
      无独立审核流
    end note
```
