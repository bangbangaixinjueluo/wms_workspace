# 结算方式能力图

```mermaid
flowchart TD
    A["结算方式主数据
    wh_settlement"] --> B["主数据 CRUD"]
    A --> C["按 settleNo + bizType 查询"]
    A --> D["按名称查询"]
    A --> E["校验转欠/结价存在"]

    C --> F["销售/退货/来料/维修 结算明细"]
    D --> F
    E --> F

    note right of A
      无独立审核态
      属于业务结算链的字典层
    end note
```
