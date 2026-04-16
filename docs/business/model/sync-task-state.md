# 接口同步任务状态图

```mermaid
flowchart TD
    A["业务模块发起同步"] --> B["wh_sync_task"]
    B --> C["请求痕迹 request* / result*"]
    B --> D["老庙回调 callback*"]
    D --> E["回写业务单据/共享单品/结算单"]
```
