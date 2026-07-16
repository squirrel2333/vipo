# Evaluation Platform

## 服务对象

算法团队

## 核心职责

- 模型效果评测（离线/在线）
- Benchmark 管理
- 回归测试（Regression Test）
- 数据集评测
- 推理性能评测（Latency、Throughput）
- A/B Evaluation
- 自动评测报告

## 最终能力

**自动评测、持续验证模型质量**

> 注意：评估不仅限于模型精度，还应覆盖推理性能和 A/B 效果，与 Training、Inference 构成完整闭环。

## 包含的平台模块

| 模块 | 职责 |
|------|------|
| [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | 评测 Pipeline 编排 |
| [[AI Infra/Modules/Dataset Management/Dataset Management\|Dataset Management]] | 评测数据集管理 |
| [[AI Infra/Modules/Evaluation & Benchmark/Evaluation & Benchmark\|Evaluation & Benchmark]] | 评测指标与对比 |
| [[AI Infra/Modules/Artifact Management/Artifact Management\|Artifact Management]] | 评测结果产物管理 |
| [[AI Infra/Modules/Observability/Observability\|Observability]] | 评测监控 |

## 相关平台

- ← [[AI Infra/Platforms/Training Platform\|Training Platform]]：模型输入
- ← [[AI Infra/Platforms/Inference Platform\|Inference Platform]]：推理效果输入
- → [[AI Infra/Platforms/Data Platform\|Data Platform]]：Bad Case 回流