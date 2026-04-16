# 客锁管理实体图
```mermaid
flowchart LR
    ORDER["wh_customer_order"] --> ITEM["wh_item"]
    ITEM --> ITEMLOG["wh_item_log"]
    ITEM --> PAGE["customer-lock 视图/导出"]
```
