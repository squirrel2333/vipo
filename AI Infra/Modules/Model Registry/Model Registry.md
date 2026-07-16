# Model Registry

## 主要职责

模型版本管理与发布管理。

- 模型版本注册
- Stage 管理（Staging / Production / Archived）
- 模型元数据
- 模型审批与发布流程
- Canary 发布关联

## 涉及平台

- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]

## 关键技术

本模块的核心能力由 [[AI Infra/Modules/Experiment Tracking/MLflow/MLflow|MLflow]] 的 Model Registry 组件提供。

## 关键概念

- Model Version
- Stage（Staging / Production / Archived）
- Canary Release
- Model Approval

## 相关模块

- [[AI Infra/Modules/Experiment Tracking/Experiment Tracking|Experiment Tracking]]：MLflow 实验管理
- [[AI Infra/Modules/Serving Runtime/Serving Runtime|Serving Runtime]]：模型部署
- [[AI Infra/Modules/Traffic & Autoscaling/Traffic & Autoscaling|Traffic & Autoscaling]]：灰度发布