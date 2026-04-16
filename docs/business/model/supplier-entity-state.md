# 供应商档案能力图
```mermaid
flowchart TD
    A["供应商建档"] --> B["校验编号/代码/名称唯一"]
    B --> C["绑定或自动创建财务账户"]
    C --> D["写入供应商主档"]

    D --> E["更新供应商"]
    E --> F["同步更新账户编码/类型/名称"]
    E --> G["记录变更日志"]

    D --> H["状态启停 updateSupplierStatus"]
    D --> I["导入 importExcel"]
    I --> J["按编号/名称匹配新增或覆盖"]
    I --> K["同步创建或更新账户"]

    D --> L["删除 deleteSupplier"]
    L --> M["校验无开放钱包"]
    M --> N["删除供应商主档"]

    note right of D
      无 BPM
      无待审批缓存
    end note
```
