# Inference Platform

## 服务对象

算法团队、业务团队

## 核心职责

- 模型部署与版本管理
- 弹性扩缩容（基于流量/延迟）
- 灰度发布、Canary、A/B Testing
- 流量路由与管理
- 推理性能优化（Latency / Throughput）
- 模型热更新与回滚

## 最终能力

**模型一键部署、一键发布**

## 包含的平台模块

| 模块 | 职责 |
|------|------|
| [[AI Infra/Modules/Model Registry/Model Registry\|Model Registry]] | 模型版本管理 |
| [[AI Infra/Modules/Serving Runtime/Serving Runtime\|Serving Runtime]] | 推理引擎 |
| [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | 部署 Pipeline |
| [[AI Infra/Modules/Scheduler/Scheduler\|Scheduler]] | 推理资源调度 |
| [[AI Infra/Modules/Traffic & Autoscaling/Traffic & Autoscaling\|Traffic & Autoscaling]] | 灰度发布、弹性伸缩 |
| [[AI Infra/Modules/Observability/Observability\|Observability]] | 推理监控 |

## 相关平台

- ← [[AI Infra/Platforms/Training Platform\|Training Platform]]：模型输入
- → [[AI Infra/Platforms/Evaluation Platform\|Evaluation Platform]]：推理效果评测