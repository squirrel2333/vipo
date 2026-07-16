# Dataset Management

## 主要职责

数据集管理与版本控制，确保数据**可复现**。

- 数据集注册与发现
- 数据版本控制
- 数据格式管理（Parquet / Iceberg / Delta Lake）
- 数据生命周期管理
- 数据标签与搜索

## 涉及平台

- [[AI Infra/Platforms/Data Platform|Data Platform]]
- [[AI Infra/Platforms/Training Platform|Training Platform]]
- [[AI Infra/Platforms/Evaluation Platform|Evaluation Platform]]

## 关键技术

| 技术 | 定位 |
|------|------|
| [[AI Infra/Modules/Dataset Management/LakeFS/LakeFS\|LakeFS]] | Git-like 数据版本控制 |
| [[AI Infra/Modules/Dataset Management/Iceberg/Iceberg\|Iceberg]] | 数据湖表格式 |
| [[AI Infra/Modules/Dataset Management/Delta Lake/Delta Lake\|Delta Lake]] | 数据湖表格式 |
| [[AI Infra/Modules/Dataset Management/Hudi/Hudi\|Hudi]] | 数据湖表格式 |

## 关键概念

- Versioning（数据版本）
- Parquet
- Time Travel
- Branch / Tag / Commit（LakeFS Git 语义）
- ACID 事务

## 相关模块

- [[AI Infra/Modules/Metadata & Lineage/Metadata & Lineage|Metadata & Lineage]]：数据血缘
- [[AI Infra/Modules/Data Quality/Data Quality|Data Quality]]：数据质量
- [[AI Infra/Modules/Storage/Storage|Storage]]：底层存储