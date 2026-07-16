# Checkpoint Management

## 主要职责

训练自动保存与恢复，确保训练**失败可恢复**。

- 自动保存 Checkpoint
- 训练恢复（Resume）
- 分布式 Checkpoint（DDP / FSDP / DeepSpeed）
- Snapshot 管理
- Checkpoint 清理策略
- 容灾策略

## 涉及平台

- [[AI Infra/Platforms/Training Platform|Training Platform]]

## 关键概念

- Checkpoint / Snapshot
- Resume（断点续训）
- Distributed Checkpoint（分布式保存）
- Async Checkpointing
- Checkpoint 一致性

## 相关模块

- [[AI Infra/Modules/Artifact Management/Artifact Management|Artifact Management]]：Checkpoint 存储
- [[AI Infra/Modules/Experiment Tracking/Experiment Tracking|Experiment Tracking]]：Checkpoint 与实验关联
- [[AI Infra/Modules/Scheduler/Scheduler|Scheduler]]：资源中断与恢复