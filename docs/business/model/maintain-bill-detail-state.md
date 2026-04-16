# 维修单详情状态/能力图

```mermaid
flowchart TD
    A["维修单明细<br/>replyStatus=0 初始"] --> B["replyStatus=1 维修中"]
    B --> C["replyStatus=2 部分到柜"]
    C --> D["replyStatus=3 已到柜"]
    B --> D
    D --> E["replyStatus=4 已完结"]
    B --> F["replyStatus=5 QC中"]
    B --> G["replyStatus=6 编货中"]
    C --> E
    D --> E
    F --> E
    G --> E

    H["updateStepLocal"] --> G
    I["finalization"] --> E
    J["维修入库单累计"] --> C
    K["维修单/维修入库/维修出库聚合"] --> B
    L["createItemEntryDetailVOByIds"] --> M["生成单件入库来源"]
    N["createItemAdjustDetailVOByIds"] --> O["生成单件调整来源"]
```

## 说明
1. 维修单详情没有自己的 `billStatus` 审核流，核心状态是明细级 `replyStatus`。
2. `finalization(...)` 是终态入口，会把明细直接推进到 `COMPLETED`。
3. “编货中”不是通用流转节点，而是 `updateStepLocal(...)` 在仓库名称命中 `"编货仓"` 时硬编码写入的状态。
