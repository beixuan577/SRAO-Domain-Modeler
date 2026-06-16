# 制造业领域模型

## 核心概念词典

| 概念 | 类型 | 定义 | 关键属性 |
|------|------|------|----------|
| 订单 | 实体 | 客户下达的生产请求 | 订单号、产品型号、数量、交期、优先级 |
| 工单 | 实体 | 订单拆分后的生产任务 | 工单号、工序、产线、计划时间 |
| 产线 | 实体 | 生产执行单元 | 产线ID、产能、当前状态、设备列表 |
| 设备 | 实体 | 产线上的生产设备 | 设备ID、型号、健康度、维护周期 |
| 物料 | 实体 | 生产所需的原料/半成品 | 物料号、批次、库存量、供应商 |
| 质检记录 | 实体 | 产品质量检测结果 | 检测项、结果值、判定、时间 |
| 预警事件 | 事件 | 异常触发的通知 | 类型、等级、来源、时间 |
| 工艺参数 | 实体 | 生产过程的控制参数 | 参数名、目标值、容差范围 |

## 实体关系

```
[订单] --1:N--> [工单]
[工单] --N:1--> [产线]
[产线] --1:N--> [设备]
[设备] --1:N--> [传感器数据]
[工单] --1:N--> [质检记录]
[工单] --N:M--> [物料]
[物料] --N:1--> [供应商]
[传感器数据] --触发--> [预警事件]
[质检记录] --触发--> [预警事件]
```

## 工作流模板

### WF-MFG-001: 订单排产

```yaml
trigger: "新订单创建"
steps:
  - id: s1
    name: 解析订单
    role: 订单解析Agent
    input: 订单原始数据
    output: 结构化订单信息
    next: [s2, s3]
  - id: s2
    name: 检查库存
    role: 库存查询Agent
    input: 物料清单
    output: 物料可用性报告
    next: [s4]
  - id: s3
    name: 计算产能
    role: 产能模拟Agent
    input: 产线状态 + 工艺参数
    output: 产能评估报告
    next: [s4]
  - id: s4
    name: 生成排产方案
    role: 排产优化Agent
    input: 订单信息 + 库存报告 + 产能报告
    output: 排产甘特图
```

### WF-MFG-002: 设备预测维护

```yaml
trigger: "周期性（每小时）或传感器异常"
steps:
  - id: s1
    name: 采集设备数据
    role: IoT采集Agent
    input: 振动/温度/电流传感器
    output: 时序数据流
    next: [s2]
  - id: s2
    name: 异常检测
    role: FFT分析Agent
    input: 时序数据
    output: 频谱异常标记
    next: [s3]
  - id: s3
    name: 剩余寿命预测
    role: PHM Agent
    input: 异常标记 + 历史数据
    output: RUL估计 + 置信区间
    next: [s4]
  - id: s4
    name: 派发工单
    role: 工单生成Agent
    input: RUL + 维护策略
    output: 维护工单
```

### WF-MFG-003: 质量追溯

```yaml
trigger: "质检不合格"
steps:
  - id: s1
    name: 缺陷检测
    role: 视觉检测Agent
    input: 产品图像
    output: 缺陷位置 + 类型
    next: [s2, s3]
  - id: s2
    name: 追溯原料批次
    role: 区块链溯源Agent
    input: 产品批次号
    output: 原料批次链
    next: [s4]
  - id: s3
    name: 分析根因
    role: 根因分析Agent
    input: 缺陷特征 + 工艺参数
    output: 可能原因排序
    next: [s4]
  - id: s4
    name: 生成报告
    role: 报告生成Agent
    input: 追溯结果 + 根因分析
    output: 质量追溯报告
```

## 典型KPI

| KPI | 基线 | 目标 |
|-----|------|------|
| 漏检率 | 5% | <0.5% |
| 设备停机时间 | 40h/月 | <8h/月 |
| 排产响应时间 | 4小时 | <30分钟 |
| 质量追溯时间 | 2天 | <1小时 |
