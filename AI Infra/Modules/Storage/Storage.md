# Storage

## 主要职责

数据与模型存储基础设施。

- 对象存储（Object Storage）
- 块存储（Block Storage）
- CSI / PVC 管理
- 存储性能优化
- 数据生命周期管理

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Inference Platform|Inference Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Storage/MinIO/MinIO\|MinIO]] | 高性能对象存储 |
| [[AI Infra/Modules/Storage/Ceph/Ceph\|Ceph]] | 分布式存储系统 |

## 关键概念

- S3 API
- Object Storage（对象存储 vs 块存储）
- CSI（Container Storage Interface）
- PVC（Persistent Volume Claim）
- OSS（阿里云对象存储）

## 相关模块

- [[AI Infra/Modules/Artifact Management/Artifact Management|Artifact Management]]：产物存储
- [[AI Infra/Modules/Dataset Management/Dataset Management|Dataset Management]]：数据存储