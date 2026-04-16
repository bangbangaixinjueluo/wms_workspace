# SKU 规则主数据状态图

```mermaid
flowchart TD
    A["设计元素主数据"] --> C["SKU / newsku / updatesku 校验与映射"]
    B["生产工艺主数据"] --> C
    A --> D["设计元素 1/2/3/4 规则来源"]
    B --> E["工艺编号/名称格式化与导入校验"]
```
