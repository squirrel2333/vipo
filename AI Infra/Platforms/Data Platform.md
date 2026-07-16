# Data Platform

## 服务对象

数据团队、算法团队

## 核心职责

- 数据采集与接入
- ETL Pipeline
- 数据标注管理
- Dataset 管理与版本控制
- Bad Case 回流
- 数据权限与合规

## 最终能力

**数据可追踪、可回滚、可复现**

## 包含的平台模块

| 模块 | 职责 |
|------|------|
| [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | 数据 Pipeline 编排 |
| [[AI Infra/Modules/Dataset Management/Dataset Management\|Dataset Management]] | 数据版本管理 |
| [[AI Infra/Modules/Metadata & Lineage/Metadata & Lineage\|Metadata & Lineage]] | 数据血缘追踪 |
| [[AI Infra/Modules/Artifact Management/Artifact Management\|Artifact Management]] | 数据产物管理 |
| [[AI Infra/Modules/Storage/Storage\|Storage]] | 数据存储 |
| [[AI Infra/Modules/Observability/Observability\|Observability]] | 数据 Pipeline 监控 |
| [[AI Infra/Modules/Scheduler/Scheduler\|Scheduler]] | 数据任务资源调度 |
| [[AI Infra/Modules/Data Quality/Data Quality\|Data Quality]] | 数据质量校验 |
| [[AI Infra/Modules/Feature Store/Feature Store\|Feature Store]] | 特征存储 |

## 相关平台

- → [[AI Infra/Platforms/Training Platform\|Training Platform]]：训练数据供给
- ← [[AI Infra/Platforms/Evaluation Platform\|Evaluation Platform]]：Bad Case 回流