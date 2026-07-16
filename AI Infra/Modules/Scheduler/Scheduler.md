# Scheduler

## 主要职责

CPU/GPU 资源调度与管理，是 AI 平台的**资源底座**。

- GPU 资源池管理
- Quota / 配额管理
- 抢占调度（Preemption）
- Gang Scheduling（All-or-Nothing）
- GPU 拓扑感知调度（NUMA、拓扑亲和性）
- GPU 共享与 MIG
- 多租户资源隔离

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Scheduler/Kubernetes Scheduler/Kubernetes Scheduler\|Kubernetes Scheduler]] | K8s 默认调度器 |
| [[AI Infra/Modules/Scheduler/Volcano/Volcano\|Volcano]] | 批调度 / Gang Scheduling |
| [[AI Infra/Modules/Scheduler/Kueue/Kueue\|Kueue]] | 资源队列 / Quota 管理 |
| [[AI Infra/Modules/Scheduler/GPU Operator/GPU Operator\|GPU Operator]] | NVIDIA GPU 管理 |

## 关键概念

- Gang Scheduling：all-or-nothing 调度
- MIG（Multi-Instance GPU）
- NUMA 亲和性
- RDMA（Remote Direct Memory Access）
- NCCL（NVIDIA Collective Communications Library）
- Device Plugin
- Topology Manager

## 相关模块

- [[AI Infra/Modules/Workflow Engine/Workflow Engine|Workflow Engine]]：Workflow 调度依赖
- [[AI Infra/Modules/Storage/Storage|Storage]]：CSI / PVC 存储调度