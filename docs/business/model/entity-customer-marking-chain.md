# 客户字印实体图
```mermaid
flowchart LR
    CUSTOMER["wh_customer"] --> MARKING["wh_customer_marking"]
    MARKING --> ORDER["wh_customer_order"]
    MARKING --> PACK["wh_pack"]
    MARKING --> SALE["wh_sale_bill"]
```
