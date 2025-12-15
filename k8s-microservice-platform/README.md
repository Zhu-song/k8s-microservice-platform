# ☸️ K8s Microservice Automation Platform
# 基于 Kubernetes 的企业级微服务自动化运维平台

<div align="center">
  <img src="docs/logo.png" alt="Logo" width="120" height="120">

  <h3 align="center">构建高可用、可观测、自动化的云原生基础设施</h3>

  <p align="center">
    从 Python 单体到 Spring Boot 微服务的架构转型实战案例
    <br />
    <a href="docs/architecture.png"><strong>查看架构图 »</strong></a>
    <br />
    <br />
    <a href="#-快速开始">快速开始</a>
    ·
    <a href="#-故障排查">故障排查</a>
    ·
    <a href="https://github.com/yourname/repo/issues">报告 Bug</a>
  </p>
</div>

<div align="center">

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.26+-326ce5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-LTS-d24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-e6522c?style=for-the-badge&logo=prometheus&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-2.7-6db33f?style=for-the-badge&logo=spring-boot&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)

</div>

---

## 📋 目录 (Table of Contents)

- [📖 项目背景](#-项目背景)
- [🏗️ 系统架构](#-系统架构)
- [📸 仪表盘展示](#-仪表盘展示)
- [✨ 核心特性](#-核心特性)
- [🛠️ 环境依赖](#-环境依赖)
- [🚀 快速开始](#-快速开始)
- [⚙️ 配置说明](#-配置说明)
- [🔍 故障排查](#-故障排查)
- [📊 项目业绩](#-项目业绩)
- [🤝 贡献指南](#-贡献指南)

---

## 📖 项目背景

本项目源于企业内部真实的架构转型需求。团队原有的 **Python 单体应用** 随着业务扩张，逐渐暴露出**部署周期长(2周)**、**扩缩容响应慢**、**故障定位困难**等核心痛点。

本项目旨在构建一套基于 **Kubernetes** 的现代化运维平台，实现：
1.  **架构解耦**：将单体拆分为基于 **Spring Boot** 的微服务。
2.  **发布自动化**：通过 CI/CD 流水线将发布时间从数小时缩短至分钟级。
3.  **全栈监控**：引入 Prometheus + EFK，实现对基础设施与业务的全链路可观测性。

---

## 🏗️ 系统架构

本项目采用经典的云原生分层架构设计：

```mermaid
graph TD
    subgraph "DevSecOps 域"
        Git[GitLab 代码库] -->|Webhook| Jenkins[Jenkins 流水线]
        Jenkins -->|Maven| Build[构建 & 单元测试]
        Jenkins -->|Docker| Image[镜像打包]
        Image -->|Push| Harbor[私有镜像仓库]
    end

    subgraph "Kubernetes 生产集群"
        direction TB
        Ingress[Nginx Ingress] --> Service
        Service --> Pod1[Spring Boot App V1]
        Service --> Pod2[Spring Boot App V2]
        
        HPA[HPA 自动扩缩容] -.->|监控 CPU/Mem| Pod1
    end

    subgraph "可观测性平台"
        Prometheus[Prometheus 监控] -->|Pull| Pod1
        Fluentd[Fluentd 日志采集] -->|Collect| Pod1
        Grafana[Grafana 看板] --> Prometheus
        Kibana[Kibana 日志检索] --> ElasticSearch
    end

    Jenkins -->|Helm Upgrade| Kubernetes生产集群
