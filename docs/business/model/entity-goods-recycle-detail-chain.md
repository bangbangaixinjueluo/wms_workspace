# 成品转料明细实体图
```mermaid
flowchart LR
    BILL["wh_goods_recycle_bill"] --> DETAIL["wh_goods_recycle_detail"]
    DETAIL --> SKU["wh_sku"]
    DETAIL --> REPORT["明细分页/导出/汇总"]
```
