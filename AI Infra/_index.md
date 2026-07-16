# AI Infra 知识库

> 我的目标不是成为 Kubernetes 专家，也不是 Argo 专家，更不是某个框架的使用者。
>
> 我的目标是成为 **AI Platform Engineer**——设计并建设 AI 研发平台，让数据、训练、推理、资源和研发流程高效协同，使算法团队能够专注于模型本身。

---

## 平台与模块

本知识库按两层组织：

| 层级 | 目录 | 说明 |
|------|------|------|
| **业务平台** | [[AI Infra/Platforms/Data Platform\|Platforms]] | 公司需要建设哪些平台，分析需求和功能 |
| **平台模块** | [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Modules]] | 平台的技术能力单元，深入具体技术实现 |

## AI 研发闭环

```
        Data Platform
              │
              ▼
        Training Platform
              │
              ▼
        Inference Platform
              │
              ▼
        Evaluation Platform
              │
              └──────────────┐
                             ▼
                    Data Platform（Bad Case 回流）
```

## 平台总览

| 平台 | 服务对象 | 核心职责 | 最终能力 |
|------|---------|---------|---------|
| [[AI Infra/Platforms/Data Platform\|Data Platform]] | 数据、算法 | 数据采集、ETL、标注、Dataset管理、Bad Case回流 | 数据可追踪、可回滚、可复现 |
| [[AI Infra/Platforms/Training Platform\|Training Platform]] | 算法 | 一键训练、实验管理、Checkpoint、分布式训练 | 训练自动化、实验可管理、失败可恢复 |
| [[AI Infra/Platforms/Inference Platform\|Inference Platform]] | 算法、业务 | 模型部署、弹性扩缩容、灰度发布、流量管理 | 模型一键部署、一键发布 |
| [[AI Infra/Platforms/Evaluation Platform\|Evaluation Platform]] | 算法 | Benchmark、模型评测、Regression Test、自动报告 | 自动评测、持续验证模型质量 |

## 模块总览

| 模块 | 主要职责 | 涉及平台 |
|------|---------|---------|
| [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | 任务编排、Pipeline | 全部 |
| [[AI Infra/Modules/Scheduler/Scheduler\|Scheduler]] | CPU/GPU资源调度、Quota、抢占 | 全部 |
| [[AI Infra/Modules/Dataset Management/Dataset Management\|Dataset Management]] | Dataset管理、数据版本 | Data、Training、Evaluation |
| [[AI Infra/Modules/Metadata & Lineage/Metadata & Lineage\|Metadata & Lineage]] | 元数据、血缘追踪 | Data、Training |
| [[AI Infra/Modules/Artifact Management/Artifact Management\|Artifact Management]] | 模型、Checkpoint、数据产物管理 | 全部 |
| [[AI Infra/Modules/Experiment Tracking/Experiment Tracking\|Experiment Tracking]] | 实验管理、超参、指标 | Training |
| [[AI Infra/Modules/Checkpoint Management/Checkpoint Management\|Checkpoint Management]] | 自动保存、恢复训练 | Training |
| [[AI Infra/Modules/Model Registry/Model Registry\|Model Registry]] | 模型版本管理、发布管理 | Training、Inference |
| [[AI Infra/Modules/Serving Runtime/Serving Runtime\|Serving Runtime]] | 模型推理 | Inference |
| [[AI Infra/Modules/Traffic & Autoscaling/Traffic & Autoscaling\|Traffic & Autoscaling]] | 灰度发布、弹性扩缩容 | Inference |
| [[AI Infra/Modules/Evaluation & Benchmark/Evaluation & Benchmark\|Evaluation & Benchmark]] | 自动评测、回归测试 | Evaluation |
| [[AI Infra/Modules/Observability/Observability\|Observability]] | 日志、监控、Tracing | 全部 |
| [[AI Infra/Modules/Storage/Storage\|Storage]] | 数据与模型存储 | 全部 |
| [[AI Infra/Modules/Data Quality/Data Quality\|Data Quality]] | 数据质量检查 | Data |
| [[AI Infra/Modules/Feature Store/Feature Store\|Feature Store]] | 特征管理 | Data、Training |

## 学习路线

参见 [[AI Infra/Learning Roadmap\|Learning Roadmap]]

## 核心原则

1. **先投资稳定的底层能力，再追风口** — 技术栈会变，但 AI 研发平台的核心逻辑不变
2. **知道"为什么"比"怎么推导"更重要** — 平台工程不需要推导论文公式，但需要理解算法为什么需要这个能力
3. **学习目标不是"会用"，而是"设计平台"**
4. **工作应该抢"覆盖面大的项目"，而不是"某个技术栈"**

---

*创建于 2026-07-15，基于与 GPT 的深度对话*