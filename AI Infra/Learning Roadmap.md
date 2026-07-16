# Learning Roadmap

## AI Infra 学习路线（三年规划）

```
Platforms（业务平台）
        │
        ▼
Modules（平台模块）
        │
        ▼
Technologies（具体技术）
```

## 学习策略

### 自上而下

1. 先理解 **业务平台**（Data → Training → Inference → Evaluation）的闭环
2. 再深入每个平台所需的 **平台模块**（Workflow、Scheduler、Serving 等）
3. 最后掌握模块下的 **具体技术**（Argo、vLLM、Volcano 等）

### 学习顺序

| 步骤 | 模块 | 理由 |
|------|------|------|
| 1 | [[AI Infra/Modules/Workflow Engine/Workflow Engine\|Workflow Engine]] | AI 研发流程的入口，编排一切 |
| 2 | [[AI Infra/Modules/Storage/Storage\|Storage]] + [[AI Infra/Modules/Scheduler/Scheduler\|Scheduler]] | 基础设施底座 |
| 3 | [[AI Infra/Modules/Dataset Management/Dataset Management\|Dataset Management]] | 数据是 AI 的输入 |
| 4 | [[AI Infra/Modules/Experiment Tracking/Experiment Tracking\|Experiment Tracking]] | 训练实验管理 |
| 5 | [[AI Infra/Modules/Checkpoint Management/Checkpoint Management\|Checkpoint Management]] | 训练容错 |
| 6 | [[AI Infra/Modules/Serving Runtime/Serving Runtime\|Serving Runtime]] | 模型推理 |
| 7 | [[AI Infra/Modules/Traffic & Autoscaling/Traffic & Autoscaling\|Traffic & Autoscaling]] | 推理运维 |
| 8 | [[AI Infra/Modules/Observability/Observability\|Observability]] | 全链路监控 |
| 9 | [[AI Infra/Modules/Evaluation & Benchmark/Evaluation & Benchmark\|Evaluation & Benchmark]] | 模型评测 |
| 10 | 其余模块（Metadata、Artifact、Model Registry、Data Quality、Feature Store） | 按需深入 |

## 学习原则

1. **基本不用看论文** — 平台工程需要知道"为什么"，不需要推导公式
2. **知道"为什么"比"怎么推导"更重要**
3. **学习目标不是"会用"，而是"设计平台"**
4. **先投资稳定的底层能力，再追风口**

## 需要掌握的算法知识程度

| 内容 | 需要掌握程度 |
|------|------------|
| DDP | 为什么需要、通信方式、资源需求 |
| FSDP | 为什么省显存、适用场景 |
| DeepSpeed ZeRO | Stage 1/2/3 的区别 |
| NCCL | AllReduce、通信瓶颈、拓扑影响 |
| Checkpoint | 保存什么、恢复流程、容灾策略 |
| AMP / BF16 | 为什么更快、更省显存 |
| Gradient Accumulation | 为什么能模拟大 Batch |
| DataLoader | 为什么可能成为瓶颈 |

做到能和算法同学正常讨论，就够了。