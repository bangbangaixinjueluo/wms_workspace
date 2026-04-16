# 客户档案能力图
```mermaid
flowchart TD
    A["客户建档"] --> B["顶级客户<br/>status=DISABLE"]
    A --> C["子客户<br/>level=2"]

    B --> D["写入 biz_cache"]
    D --> E["提交审批 submitProcess/batchSubmit"]
    E --> F["PROCESS"]
    F --> G["审批通过 APPROVE"]
    F --> H["审批拒绝 REJECT"]

    G --> I["启用客户"]
    G --> J["激活/更新财务账户"]
    G --> K["初始化默认字印"]
    G --> L["同步需求单客户名"]
    G --> M["记录变更日志/同步钱包名"]

    I --> N["可生成商城账号"]
    I --> O["可修改地址"]

    C --> P["继承父客户业务属性"]
    C --> Q["直接 APPROVE"]

    note right of C
      子客户不走 BPM
      默认不单独建账户
    end note
```
