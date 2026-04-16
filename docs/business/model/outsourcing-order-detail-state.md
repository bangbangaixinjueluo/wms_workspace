# 委外加工订单明细能力图

```mermaid
flowchart TD
    A["委外明细
    wh_outsourcing_order_detail"] --> B["明细分页/导出"]
    A --> C["来货记录
    selectIncomingRecords"]
    A --> D["待单件入库池
    getWaitEntryDetail"]
    A --> E["转成品入库池
    inboundOptions"]
    A --> F["工厂导出"]
    A --> G["采购汇总"]

    C --> H["订单核销明细
    wh_order_writeoff_detail"]
    D --> I["单件入库明细
    wh_entry_bill_detail"]
    E --> J["convertInboundNo is null
    orderStatus=DELIVERED"]

    note right of A
      无独立审核态
      属于委外主链的辅助查询/来源池层
    end note
```
