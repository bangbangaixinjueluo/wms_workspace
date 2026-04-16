# 收款分账明细实体图
```mermaid
flowchart LR
    RECEIVABLE["wh_receivable_bill"] --> DIVIDE["wh_receivable_divide_bill"]
    DIVIDE --> WALLET["钱包归属结果"]
```
