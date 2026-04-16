# 小包明细实体图
```mermaid
flowchart LR
    TRAY["wh_tray"] --> DETAIL["wh_tray_detail"]
    DETAIL --> ITEM["wh_item"]
    DETAIL --> PACKDETAIL["wh_pack_detail"]
```
