# Feature Store

## 主要职责

特征管理与服务，连接数据与训练。

- 特征定义与注册
- 离线特征服务（Training）
- 在线特征服务（Inference）
- 特征一致性校验
- 特征版本管理

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Feature Store/Feast/Feast\|Feast]] | 开源 Feature Store |

## 关键概念

- Feature Definition
- Offline Store（离线特征）
- Online Store（在线特征）
- Feature Consistency（特征一致性）
- Point-in-Time Correctness

## 相关模块

- [[AI Infra/Modules/Dataset Management/Dataset Management|Dataset Management]]：数据集
- [[AI Infra/Modules/Data Quality/Data Quality|Data Quality]]：特征质量