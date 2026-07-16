# Serving Runtime

## 主要职责

模型推理引擎，是模型从训练到产出的**最后一公里**。

- 模型加载与推理
- 推理性能优化（Latency / Throughput）
- 模型量化与加速
- 多模型管理
- 批处理推理

## 涉及平台

- [[AI Infra/Platforms/Inference Platform|Inference Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Serving Runtime/vLLM/vLLM\|vLLM]] | 高性能 LLM 推理（PagedAttention） |
| [[AI Infra/Modules/Serving Runtime/SGLang/SGLang\|SGLang]] | 高效 LLM 推理框架 |
| [[AI Infra/Modules/Serving Runtime/Triton/Triton\|Triton]] | NVIDIA 推理服务器 |
| [[AI Infra/Modules/Serving Runtime/TensorRT-LLM/TensorRT-LLM\|TensorRT-LLM]] | NVIDIA LLM 推理优化 |
| [[AI Infra/Modules/Serving Runtime/Ray Serve/Ray Serve\|Ray Serve]] | 通用模型 Serving 框架 |
| [[AI Infra/Modules/Serving Runtime/KServe/KServe\|KServe]] | K8s 原生模型 Serving |

## 关键概念

- Latency / Throughput
- Batching（Continuous / Dynamic Batching）
- KV Cache
- PagedAttention
- Quantization（量化）
- Speculative Decoding

## 相关模块

- [[AI Infra/Modules/Traffic & Autoscaling/Traffic & Autoscaling|Traffic & Autoscaling]]：流量与弹性
- [[AI Infra/Modules/Model Registry/Model Registry|Model Registry]]：模型版本