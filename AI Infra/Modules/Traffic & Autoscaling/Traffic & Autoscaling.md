# Traffic & Autoscaling

## 主要职责

灰度发布、流量管理与弹性扩缩容。

- 灰度发布（Canary / Blue-Green）
- A/B Testing
- 流量路由与分流
- 弹性扩缩容（基于指标）
- 流量限流与熔断

## 涉及平台

- [[AI Infra/Platforms/Inference Platform|Inference Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Traffic & Autoscaling/KEDA/KEDA\|KEDA]] | 事件驱动弹性伸缩 |
| [[AI Infra/Modules/Traffic & Autoscaling/Istio/Istio\|Istio]] | 服务网格 / 流量管理 |

## 关键概念

- HPA（Horizontal Pod Autoscaler）
- KEDA（Kubernetes Event-driven Autoscaling）
- Gateway API
- Canary / A/B Testing
- Circuit Breaker（熔断）
- Rate Limiting（限流）

## 相关模块

- [[AI Infra/Modules/Serving Runtime/Serving Runtime|Serving Runtime]]：推理引擎
- [[AI Infra/Modules/Model Registry/Model Registry|Model Registry]]：模型版本