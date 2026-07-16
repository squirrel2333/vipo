# Artifact Management

## 主要职责

模型、Checkpoint、数据产物的生命周期管理。

- 模型产物存储与版本
- Checkpoint 存储
- 数据产物管理
- 产物下载 / 分发
- 产物清理与归档

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键概念

- Object Storage（对象存储）
- S3 / MinIO / OSS

## 存储后端

参见 [[AI Infra/Modules/Storage/Storage|Storage]] 模块，具体技术：
- [[AI Infra/Modules/Storage/MinIO/MinIO|MinIO]]
- [[AI Infra/Modules/Storage/Ceph/Ceph|Ceph]]

## 相关模块

- [[AI Infra/Modules/Storage/Storage|Storage]]：底层存储
- [[AI Infra/Modules/Checkpoint Management/Checkpoint Management|Checkpoint Management]]：Checkpoint 策略
- [[AI Infra/Modules/Model Registry/Model Registry|Model Registry]]：模型版本