# Experiment Tracking

## 主要职责

实验管理、超参与指标追踪。

- 实验注册与组织
- 超参追踪
- 指标记录与可视化
- 实验对比
- 模型与实验关联

## 涉及平台

- [[AI Infra/Platforms/Training Platform|Training Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Experiment Tracking/MLflow/MLflow\|MLflow]] | 全流程实验管理 |
| [[AI Infra/Modules/Experiment Tracking/Weights & Biases/Weights & Biases\|Weights & Biases]] | 实验追踪与可视化 |
| [[AI Infra/Modules/Experiment Tracking/TensorBoard/TensorBoard\|TensorBoard]] | 训练可视化 |

## 关键概念

- Experiment / Run
- Hyperparameter
- Metric / Artifact
- Experiment Comparison

## 相关模块

- [[AI Infra/Modules/Model Registry/Model Registry|Model Registry]]：MLflow 同时提供 Registry 能力
- [[AI Infra/Modules/Checkpoint Management/Checkpoint Management|Checkpoint Management]]：Checkpoint 追踪