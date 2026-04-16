# 供应商应付账调整实体关系图
> 核心关注：供应商应付账调整如何把金额账、金重账分别落到钱包链

```mermaid
graph TD
    A["wh_supplier_adjust_bill\n供应商应付账调整单头"] --> B["wh_supplier_adjust_bill_detail\n调整明细"]
    B --> C["wh_supplier\n供应商"]
    C --> D["wh_account\n账户"]
    B --> E["wh_material\n材质"]
    B --> F["wh_wallet\n钱包余额"]
    B --> G["wh_wallet_log\n钱包日志"]
    B --> H["wh_wallet_daily\n钱包日汇总"]

    B -- "amount" --> F
    B -- "gold_weight + materialId + walletTypeNo + settleClass" --> F
    F --> G
    F --> H
```

## 关系说明
- 单头只负责承载日期、状态、审核人、备注等头信息。
- 真正驱动钱包动账的是明细表，不是单头汇总金额。
- 一条明细可拆成两笔钱包动作：
  - `amount`：固定走现金钱包。
  - `gold_weight`：按材质/钱包类型/结算类别定位材质钱包。
- 钱包余额、日志、日汇总由 `createWalletByAccountNew()` 在同一次服务调用里统一维护。
