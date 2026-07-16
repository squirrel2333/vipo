# Training Platform

## 服务对象

算法团队

## 核心职责

- 一键训练（Job 提交、资源分配、环境准备）
- 实验管理（超参、指标、日志）
- Checkpoint 管理与自动恢复
- 分布式训练（DDP、FSDP、DeepSpeed）
- GPU 资源调度
- 训练镜像与依赖管理

## 最终能力

**训练自动化、实验可管理、失败可恢复**

## 包含的平台模块

| 模块 | 职责 |
|------|------|
| [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | 训练 Pipeline 编排 |
| [[AI Infra/Modules/Scheduler/Scheduler\|Scheduler]] | GPU 资源调度 |
| [[AI Infra/Modules/Dataset Management/Dataset Management\|Dataset Management]] | 训练数据供给 |
| [[AI Infra/Modules/Experiment Tracking/Experiment Tracking\|Experiment Tracking]] | 实验管理 |
| [[AI Infra/Modules/Checkpoint Management/Checkpoint Management\|Checkpoint Management]] | 自动保存与恢复 |
| [[AI Infra/Modules/Artifact Management/Artifact Management\|Artifact Management]] | 模型产物管理 |
| [[AI Infra/Modules/Metadata & Lineage/Metadata & Lineage\|Metadata & Lineage]] | 训练元数据追踪 |
| [[AI Infra/Modules/Model Registry/Model Registry\|Model Registry]] | 模型版本注册 |
| [[AI Infra/Modules/Observability/Observability\|Observability]] | 训练监控 |

## 相关平台

- ← [[AI Infra/Platforms/Data Platform\|Data Platform]]：训练数据输入
- → [[AI Infra/Platforms/Inference Platform\|Inference Platform]]：模型交付部署