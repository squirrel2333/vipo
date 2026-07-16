# Observability

## 主要职责

日志、监控、Tracing，是 AI 平台的**反馈系统**。

- 日志收集与分析
- 指标监控（Prometheus）
- 分布式追踪（Tracing）
- GPU 监控
- Pipeline 监控
- 告警

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Observability/Prometheus/Prometheus\|Prometheus]] | 指标采集与告警 |
| [[AI Infra/Modules/Observability/Grafana/Grafana\|Grafana]] | 可视化面板 |
| [[AI Infra/Modules/Observability/OpenTelemetry/OpenTelemetry\|OpenTelemetry]] | 可观测性标准 |
| [[AI Infra/Modules/Observability/Loki/Loki\|Loki]] | 日志聚合 |
| [[AI Infra/Modules/Observability/Tempo/Tempo\|Tempo]] | 分布式 Tracing |
| [[AI Infra/Modules/Observability/Jaeger/Jaeger\|Jaeger]] | 分布式 Tracing |

## 关键概念

- Metrics / Logs / Traces（三大支柱）
- Prometheus Metrics
- OpenTelemetry（OTel）
- Dashboard / Alerting

## 相关模块

- [[AI Infra/Modules/Scheduler/Scheduler|Scheduler]]：GPU 监控
- [[AI Infra/Modules/Workflow Engine/Workflow Engine|Workflow Engine]]：Pipeline 监控