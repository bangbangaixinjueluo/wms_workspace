# 维修结算明细状态/依附图

```mermaid
flowchart TD
    A["维修单表单
    MaintainBillStoreVO.settleList"] --> B["维修结算明细
    wh_maintain_bill_settle"]
    B --> C["维修确认链
    ConfirmData.settleDOList"]

    C --> D["普通钱包数据
    createWalletBaseDTO"]
    C --> E["结价冲回钱包
    createCloseWalletBaseDTO"]

    D --> F["材质钱包
    按 materialNo+settleClass 聚合 settleWeight"]
    D --> G["现金钱包
    totalSettleMoney"]
    E --> H["CLOSING_PRICE 结价冲回"]

    note right of B
      无独立审核态
      生命周期依附维修单
    end note
```
