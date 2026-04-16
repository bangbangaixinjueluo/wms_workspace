---
name: openclaw-business-rag-lite
description: Use this skill when the user provides a business requirement and wants an evaluation plus solution design based on docs/business. This skill performs lightweight retrieval across the business docs, identifies impacted modules, states, fields, and writeback chains, then outputs a structured assessment instead of writing code by default.
---

# OpenClaw Business RAG Lite

这个 skill 的目标非常明确：

**输入一个业务需求，输出一份可直接用于沟通和开发前评估的“需求评估 + 方案设计”。**

默认不写代码，除非用户明确要求进入实现阶段。

## 什么时候使用

满足任一条件就用：
- 用户提出“新增一个按钮 / 校验 / 状态 / 权限 / 自动动作”
- 用户想知道一个需求会影响哪些模块、字段、状态、联动
- 用户要“先评估，不写代码”
- 用户要“先给方案设计、风险点、待确认问题”
- 用户提到 OpenClaw、skill、需求评估、方案设计、RAG、轻量检索

不要用于：
- 纯代码修 bug，且用户已经明确要直接实现
- 纯报表取数问题，不涉及业务链评估
- 与 `docs/business` 无关的通用技术问题

## 一句话工作方式

先用轻量检索定位文档，再按固定模板输出：

1. 需求理解
2. 涉及模块
3. 影响范围
4. 方案设计
5. 风险点
6. 待确认问题

## 输入要求

理想输入包含：
- 需求标题
- 业务背景
- 涉及页面或模块
- 当前行为
- 期望行为
- 生效条件
- 限制条件
- 是否要权限/日志/消息
- 最担心影响哪里

如果用户没按模板给，也照样工作：
- 先根据现有描述提炼需求
- 做必要假设
- 明确写出这些假设

详见：
- `references/input-contract.md`

## 必须遵守的文档检索顺序

不要一上来全量通读。

### 第一步：先读总入口
- `docs/business/overview.md`

### 第二步：再读总表
优先级从高到低：
- `docs/business/state-matrix.md`
- `docs/business/business-metrics.md`
- `docs/business/cross-module-writeback.md`
- `docs/business/field-semantics.md`
- `docs/business/change-impact.md`
- `docs/business/external-interactions.md`

### 第三步：命中后再读模块正文
- `docs/business/*.md`

### 第四步：必要时补图谱层
- `docs/business/model/*.md`

## 轻量检索原则

这个 skill 是“RAG lite”，不是“重型知识库”。

工作重点不是搭平台，而是稳定召回：
- 总表
- 模块正文
- 状态/实体图

推荐检索方式：
1. 先按关键词过滤候选
2. 再按轻量向量相似度召回
3. 再按文档优先级重排

优先召回的关键词类型：
- 模块名：需求单、客户订单、维修单、收款单、委外单
- 动作词：审核、反审、完结、终结、分账、结价、作废、同步
- 口径词：未到柜、未回货、核销、欠料、欠款、共享库存
- 风险词：聚合状态、累计字段、钱包、银行卡、回写、来源挂接

详细规则见：
- `references/corpus-and-metadata.md`
- `references/chunking-and-indexing.md`
- `references/retrieval-and-output.md`

## 输出规则

默认必须输出以下 6 段，不要省略：

### 1. 需求理解
- 用业务语言复述
- 翻译成系统真实模块/状态/字段

### 2. 涉及模块
- 主模块
- 上下游模块
- 相关总表

### 3. 影响范围
- 页面/按钮
- 接口
- 状态机
- 字段
- 回写链

### 4. 方案设计
- 前端改动点
- 后端改动点
- 权限点建议
- 状态与数据处理建议

### 5. 风险点
- 聚合状态风险
- 累计字段风险
- 钱包/银行卡风险
- 来源挂接或历史数据风险

### 6. 待确认问题
- 需要向客户确认的业务口径
- 需要回源码确认的技术点

详细模板见：
- `references/output-contract.md`

## 默认判断规则

### 规则 1：先评估，不写代码
除非用户明确说“开始实现”，否则停在评估和方案设计。

### 规则 2：文档优先定位，代码优先定版
文档用于快速收敛范围。
代码用于确认真实实现。

### 规则 3：遇到这些情况必须回代码
- 聚合状态
- 累计字段
- 钱包或银行卡过账
- 委外、维修、退厂、共享库存
- 导出、报表、存储过程
- 文档中已有 `⚠️`

### 规则 4：文档和代码冲突时要明确指出
不能直接忽略冲突，要写清：
- 文档口径
- 代码真实实现
- 哪个更可信

## 澄清问题策略

如果需求里有明显歧义，不要泛泛而问。
只问“会直接影响方案选型”的问题。

优先澄清：
- 生效范围
- 终态定义
- 是否允许反操作
- 是否只是按钮显示规则，还是后端状态机规则
- 是否涉及钱包、库存、委外、维修等高风险链路

详见：
- `references/clarification-rules.md`

## 最终目标

这个 skill 不是为了“回答一个问题”。

而是为了让 OpenClaw 在接到需求时，稳定做到：
- 快速定位业务域
- 不漏主模块和上下游
- 不把口径看错
- 不把聚合状态当手工状态
- 给出可落地的评估和方案设计

