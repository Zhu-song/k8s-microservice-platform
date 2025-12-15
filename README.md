# ☸️ Enterprise Cloud-Native Platform | 企业级云原生 PaaS 平台

<div align="center">
  <img src="https://kubernetes.io/images/wheel.svg" alt="Logo" width="120" height="120">

  <h1>K8s Microservice Automation & Observability Platform</h1>
  
  <p align="center">
    <strong>构建 "安全、稳定、高效" 的下一代基础设施</strong>
    <br />
    基于 Kubernetes v1.26 • Istio Ready • DevSecOps • FinOps
  </p>

  <p align="center">
    <a href="#-1-项目综述">项目背景</a> •
    <a href="#-2-系统架构">架构设计</a> •
    <a href="#-3-devsecops-流水线">安全流水线</a> •
    <a href="#-5-生产级运维手册">运维手册</a> •
    <a href="#-6-部署指南">快速开始</a>
  </p>
</div>

<div align="center">

![K8s](https://img.shields.io/badge/K8s-v1.26.3-326ce5?style=for-the-badge&logo=kubernetes)
![Docker](https://img.shields.io/badge/Container-Docker%2020.10-2496ed?style=for-the-badge&logo=docker)
![Calico](https://img.shields.io/badge/Network-Calico%20BGP-f3552e?style=for-the-badge)

![Jenkins](https://img.shields.io/badge/CI-Jenkins-d24939?style=for-the-badge&logo=jenkins)
![SonarQube](https://img.shields.io/badge/Quality-SonarQube-4e9bcd?style=for-the-badge&logo=sonarqube)
![Trivy](https://img.shields.io/badge/Security-Trivy%20Scanner-000000?style=for-the-badge)

![Prometheus](https://img.shields.io/badge/Metrics-Prometheus-e6522c?style=for-the-badge&logo=prometheus)
![Grafana](https://img.shields.io/badge/Visual-Grafana-F46800?style=for-the-badge&logo=grafana)
![ELK](https://img.shields.io/badge/Logs-EFK%20Stack-005571?style=for-the-badge&logo=elastic)

</div>

---

## 📖 1. 项目综述 (Executive Summary)

本项目源于企业真实的架构转型实战。面对业务扩张，团队将原有的 **Python 单体应用** 成功解耦并迁移至基于 **Spring Boot** 的微服务架构。

我们通过自建 Kubernetes 集群，构建了一套集 **“自动化发布、全链路监控、弹性伸缩”** 于一体的 PaaS 平台，彻底解决了旧架构中“发布周期长(2周)”、“故障定位难”、“资源利用率低”的核心痛点。

### 🏆 核心业绩指标 (Key Achievements)

| 关键指标 | 传统架构 (Legacy) | 云原生架构 (Current) | 提升幅度 |
| :--- | :--- | :--- | :--- |
| **平均发布耗时** | 45 分钟 (人工) | **3 分钟 (自动化)** | 🚀 **1500%** |
| **资源利用率** | 15% - 20% | **60% - 75%** | 💰 **300%** |
| **故障恢复 (MTTR)** | ~30 分钟 | **< 5 分钟** | 🛡️ **600%** |
| **SLA 可用性** | 99.9% | **99.95%** | 🔥 **高可用** |

---

## 🏗️ 2. 系统架构 (System Architecture)

本平台采用经典的**云原生分层治理架构**，自下而上实现了基础设施的可编程化与业务应用的敏捷化。

### 2.1 逻辑架构分层视图
我们设计了四层架构模型，确保关注点分离（Separation of Concerns）：

| 层级 | 核心组件 | 职责描述 |
| :--- | :--- | :--- |
| **接入层** | F5 / MetalLB, Nginx Ingress | 七层流量卸载、SSL 终结、路由转发及黑白名单控制。 |
| **服务层** | Spring Boot, Istio Sidecar | 承载核心业务，通过 Sidecar 实现熔断、限流与链路追踪。 |
| **平台层** | K8s API, Harbor, Jenkins | 提供调度编排、镜像分发、CI/CD 流水线及配置中心能力。 |
| **基础层** | Kubeadm, Calico, Ceph/NFS | 提供计算资源池化、扁平化容器网络及分布式存储能力。 |

### 2.2 流量治理与网络架构
摒弃 Overlay 隧道，采用 **Calico BGP** 模式，实现 Pod IP 在 VPC 内可路由，性能损耗 < 3%。

```mermaid
graph TD
    subgraph "南北向流量 (North-South)"
        Client[外部用户] -->|HTTPS| SLB[负载均衡器]
        SLB -->|TCP 80/443| Ingress[Nginx Controller]
        Ingress -->|Route Rule| Svc[K8s Service]
        Svc -->|Endpoints| Pod[业务容器]
    end

    subgraph "东西向流量 (East-West)"
        PodA[订单服务] -.->|Direct Route| PodB[库存服务]
        PodB -.->|NetworkPolicy| PodC[数据库]
    end
