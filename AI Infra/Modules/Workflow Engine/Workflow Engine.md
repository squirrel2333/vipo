# Workflow Engine

## 主要职责

任务编排与 Pipeline 管理，是 AI 研发流程的**入口**和**骨架**。

- 定义和调度 DAG 任务图
- 事件驱动触发
- 定时任务（CronWorkflow）
- 任务重试与容错
- 任务间 Artifact 传递
- ML Pipeline 编排

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Workflow Engine/Argo Workflows/Argo Workflows\|Argo Workflows]] | K8s 原生 Workflow 引擎 |
| [[AI Infra/Modules/Workflow Engine/Argo Events/Argo Events\|Argo Events]] | 事件驱动触发 |
| [[AI Infra/Modules/Workflow Engine/Temporal/Temporal\|Temporal]] | 长时间运行 Workflow（适合复杂 ML Pipeline） |
| [[AI Infra/Modules/Workflow Engine/Airflow/Airflow\|Airflow]] | 传统数据 Pipeline 编排 |
| [[AI Infra/Modules/Workflow Engine/Flyte/Flyte\|Flyte]] | ML 原生 Workflow 平台 |
| [[AI Infra/Modules/Workflow Engine/Kubeflow Pipelines/Kubeflow Pipelines\|Kubeflow Pipelines]] | K8s 上 ML Pipeline |

## 关键概念

- DAG（有向无环图）
- CronWorkflow（定时触发）
- Retry / Backoff（重试策略）
- Artifact（任务间产物传递）
- Parameter / Template（参数化）
- Step / DAG / Script Template

## 相关模块

- [[AI Infra/Modules/Scheduler/Scheduler|Scheduler]]：Workflow 需要资源调度
- [[AI Infra/Modules/Artifact Management/Artifact Management|Artifact Management]]：任务产物管理
- [[AI Infra/Modules/Observability/Observability|Observability]]：Workflow 监控