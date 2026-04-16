# 收款分账明细能力图
```mermaid
flowchart TD
    A["receivablebill 分账/取消分账"] --> B["wh_receivable_divide_bill"]
    B --> C["按 billNo 查询分账结果"]
    B --> D["分账列表/导出展示"]
```
