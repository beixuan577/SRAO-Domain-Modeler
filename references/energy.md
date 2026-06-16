# 能源行业领域模型（风电/光伏）

## 核心概念词典

| 概念 | 类型 | 定义 | 关键属性 |
|------|------|------|----------|
| 风机 | 实体 | 风力发电机组 | 风机ID、型号、额定功率、投运日期 |
| SCADA数据 | 实体 | 风机运行监控数据 | 转速、功率、温度、振动、风向 |
| 气象预报 | 实体 | 风场气象预测 | 风速、风向、温度、气压、时间粒度 |
| 功率预测 | 实体 | 发电量预测结果 | 预测值、置信区间、时间窗口 |
| 故障诊断 | 实体 | 设备故障分析结论 | 故障类型、位置、严重度、建议措施 |
| 巡检任务 | 实体 | 无人机/人工巡检记录 | 航线、图像、缺陷标记、时间 |
| 工单 | 实体 | 维护作业指令 | 工单号、类型、优先级、指派人员 |

## 实体关系

```
[风机] --1:N--> [SCADA数据]
[风机] --1:N--> [故障诊断]
[风机] --1:1--> [功率预测]
[气象预报] --输入--> [功率预测]
[SCADA数据] --触发--> [故障诊断]
[故障诊断] --生成--> [工单]
[风机] --1:N--> [巡检任务]
[巡检任务] --发现--> [故障诊断]
```

## 工作流模板

### WF-ENR-001: 风机健康监测

```yaml
trigger: "周期性（每10分钟）"
steps:
  - id: s1
    name: 读取SCADA数据
    role: 数据接入Agent
    input: 风机传感器列表
    output: 标准化时序数据
    next: [s2, s3]
  - id: s2
    name: 振动频谱分析
    role: 时频分析Agent
    input: 振动信号
    output: 频谱特征 + 异常标记
    next: [s4]
  - id: s3
    name: 温度趋势分析
    role: 时序预测Agent
    input: 温度历史
    output: 趋势预警
    next: [s4]
  - id: s4
    name: 齿轮箱故障诊断
    role: 故障诊断模型Agent
    input: 频谱 + 温度预警
    output: 故障概率 + 类型
    next: [s5]
  - id: s5
    name: 发送预警通知
    role: 通知Agent
    input: 故障诊断结果
    output: 推送消息
```

### WF-ENR-002: 发电功率预测

```yaml
trigger: "每天定时（06:00）"
steps:
  - id: s1
    name: 获取气象预报
    role: 气象数据Agent
    input: 风场坐标 + 时间范围
    output: NWP网格数据
    next: [s2]
  - id: s2
    name: 数据清洗与对齐
    role: 数据清洗Agent
    input: NWP + 历史SCADA
    output: 对齐后的训练集
    next: [s3]
  - id: s3
    name: LSTM模型预测
    role: LSTM预测Agent
    input: 对齐数据
    output: 功率预测序列
    next: [s4]
  - id: s4
    name: 不确定性量化
    role: 不确定性量化Agent
    input: 预测序列 + 模型残差
    output: 置信区间
```

### WF-ENR-003: 无人机巡检

```yaml
trigger: "计划周期 / 异常触发"
steps:
  - id: s1
    name: 规划航线
    role: 路径规划Agent
    input: 风机位置 + 巡检类型
    output: 飞行航线
    next: [s2]
  - id: s2
    name: 采集红外/可见光图像
    role: 无人机控制Agent
    input: 航线
    output: 图像集
    next: [s3]
  - id: s3
    name: 叶片缺陷识别
    role: 缺陷检测Agent
    input: 图像集
    output: 缺陷清单 + 位置
    next: [s4]
  - id: s4
    name: 三维重建（可选）
    role: 三维重建Agent
    input: 图像集 + 缺陷坐标
    output: 三维模型
```

## 典型KPI

| KPI | 基线 | 目标 |
|-----|------|------|
| 功率预测准确率 | 82% | >90% |
| 故障预警提前量 | 0（事后发现） | ≥4小时 |
| 巡检周期 | 7天/次 | 按需（异常触发） |
| 误报率 | 30% | <10% |
