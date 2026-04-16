# Corpus And Metadata

## 1. 语料范围

建议索引以下目录：

```text
docs/business/*.md
docs/business/model/*.md
```

默认排除：
- `analysis-workflow.md`
- `session-handoff.md`

原因：
- 它们适合做人工交接与阅读顺序，不适合作为高权重业务事实来源
- 但仍可以作为低优先级辅助语料

## 2. 文档分层

### A. 入口层
- `overview.md`

作用：
- 定位业务域
- 找模块入口
- 找总表入口

### B. 总表层
- `state-matrix.md`
- `business-metrics.md`
- `cross-module-writeback.md`
- `field-semantics.md`
- `change-impact.md`
- `external-interactions.md`

作用：
- 需求评估的第一跳
- 状态、口径、联动、风险的快速召回

### C. 模块正文层
- 例如 `demandorder.md`
- 例如 `receivablebill.md`
- 例如 `maintainbill.md`

作用：
- 作为模块级事实来源
- 回答模块职责、主状态、回写对象、风险点

### D. 图谱层
- `docs/business/model/*.md`

作用：
- 用于补状态、实体关系、能力边界
- 适合做“状态流转”“实体挂接”的辅助召回

## 3. 元数据建议

每个 chunk 建议包含：

| 字段 | 说明 |
|------|------|
| `id` | 全局唯一 chunk id |
| `path` | 文档路径 |
| `doc_type` | `overview/summary/module/state/entity/report` |
| `module` | 模块名，如 `demandorder` |
| `section` | 标题路径 |
| `title` | 当前 chunk 标题 |
| `content` | chunk 正文 |
| `tags` | 关键词数组 |
| `source_group` | `entry/total-table/module-doc/model-doc` |
| `priority` | 建议范围 `1-5` |

## 4. 关键词标签建议

建议至少打这几类标签：

- 业务域标签  
  `订单`、`库存`、`维修`、`委外`、`资金`、`共享库存`、`主数据`

- 动作标签  
  `创建`、`审核`、`反审`、`作废`、`完结`、`分账`、`终结`、`同步`

- 风险标签  
  `聚合状态`、`累计字段`、`钱包过账`、`银行卡`、`回写`、`导出`、`报表`

- 需求类型标签  
  `按钮显示`、`权限控制`、`状态流转`、`字段口径`、`消息通知`、`日志`

