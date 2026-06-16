---
name: srao-domain-modeler
description: 行业领域建模与需求结构化（SRAO阶段0-1）。将行业知识、业务流程、用户意图转化为标准化的领域模型和结构化需求模型（SRM）。触发词：领域建模、行业建模、需求结构化、SRAO、业务拆解、行业分析、概念本体、实体关系、工作流模板、领域知识图谱。当用户描述一个行业场景并希望将其转化为可执行的智能体系统蓝图时使用。
---

# SRAO 领域建模器

将行业知识转化为标准化领域模型和结构化需求，为智能体集群编排提供基石。

## 核心流程

```
用户描述行业场景 → 领域建模(阶段0) → 需求结构化(阶段1) → 输出SRM文档
```

## 阶段0：领域建模

### 步骤

1. **收集行业知识**：访谈用户，提取该行业的核心概念、实体、关系、事件
2. **构建概念本体**：定义实体类型、属性、关系（用ER图或概念图表达）
3. **梳理现有工作流**：将SOP/流程图/口头描述转化为标准化的泳道图
4. **提取工作流模板**：识别可复用的流程模式，抽象为参数化模板

### 输出格式

领域模型文档包含三部分：

#### 1. 领域概念词典

```markdown
| 概念 | 类型 | 定义 | 关键属性 | 关联概念 |
|------|------|------|----------|----------|
| 订单 | 实体 | 客户下达的生产请求 | 订单号/产品/数量/交期 | 客户、产线、物料 |
```

#### 2. 实体关系图（文字描述）

```
[订单] --1:N--> [工单]
[工单] --N:1--> [产线]
[工单] --1:N--> [质检记录]
[设备] --1:N--> [传感器数据]
[传感器数据] --触发--> [预警事件]
```

#### 3. 工作流模板

```yaml
workflow_template:
  name: "模板名称"
  trigger: "触发条件"
  steps:
    - id: s1
      name: "步骤名"
      role: "执行角色"
      input: "输入描述"
      output: "输出描述"
      next: [s2]        # 串行
    - id: s2
      parallel: [s2a, s2b]  # 并行
    - id: s2a
      name: "并行分支A"
    - id: s2b
      name: "并行分支B"
```

## 阶段1：需求结构化

将用户自然语言需求转化为结构化需求模型（SRM）。

### SRM格式

```yaml
structured_requirement:
  id: REQ-001
  name: "需求名称"
  description: "一句话描述"
  
  # 业务上下文
  domain: "行业名"
  sub_domain: "子领域"
  stakeholder: "利益相关方"
  
  # 目标与约束
  goals:
    - metric: "KPI名称"
      current: "当前值"
      target: "目标值"
  constraints:
    - "约束条件1"
    - "约束条件2"
  
  # 可用资源
  existing_tools: ["已有工具1", "已有工具2"]
  data_sources: ["数据源1", "数据源2"]
  human_roles: ["人工角色1"]
  
  # 工作流映射
  workflow_ref: "工作流模板ID"
  customization:
    - step: "步骤ID"
      override: "定制说明"
  
  # 验收标准
  acceptance:
    - "验收条件1"
    - "验收条件2"
```

### 需求解构规则

| 用户说法 | 映射到SRM字段 |
|----------|---------------|
| "我们要做XX行业" | domain + sub_domain |
| "目前是人工做的" | existing_tools + human_roles |
| "希望3个月内交付" | constraints |
| "准确率要到95%" | goals.metric + target |
| "已经有了XX系统" | existing_tools + data_sources |
| "出了事要能追溯" | acceptance + workflow customization |

## 行业快速启动模板

当用户提到以下行业时，直接引用对应参考文件获取预建模型：

| 行业 | 参考文件 |
|------|----------|
| 制造业 | references/manufacturing.md |
| 能源（风电/光伏） | references/energy.md |
| 医疗健康 | references/healthcare.md |
| 农业 | references/agriculture.md |
| 交通/隧道 | references/transport.md |

未列出的行业：从头建模，使用上方通用流程。

## 交付物检查清单

领域建模完成后，确认以下输出齐全：

- [ ] 领域概念词典（≥10个核心概念）
- [ ] 实体关系图（含主要关联路径）
- [ ] 至少1个工作流模板（含串行/并行标注）
- [ ] SRM文档（含目标、约束、验收标准）
- [ ] 可用资源盘点（工具/数据/人力）

若用户只需建模不需编排，到此结束。若需继续拆解为智能体图谱，引导用户使用 `srao-agent-graph` skill。
