# k8s-microservice-platform
# ☸️ Enterprise Cloud-Native Platform | 企业级云原生 PaaS 平台

<div align="center">
  <img src="https://kubernetes.io/images/wheel.svg" alt="Logo" width="120" height="120">

  <h1>K8s Microservice Automation & Observability Platform</h1>
  
  <p align="center">
    <strong>构建 "安全、稳定、高效" 的下一代基础设施</strong>
    <br />
    基于 Kubernetes v1.26 • Istio Service Mesh • DevSecOps • FinOps
  </p>

  <p align="center">
    <a href="#-系统架构">架构设计</a> •
    <a href="#-devsecops-流水线">DevSecOps</a> •
    <a href="#-day-2-运维手册">运维手册</a> •
    <a href="#-灾难恢复-dr-策略">灾难恢复</a> •
    <a href="#-容量规划">容量规划</a>
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

本项目旨在解决传统单体架构在**敏捷交付**与**高可用性**方面的瓶颈。通过引入云原生技术栈，我们不仅实现了应用容器化，更构建了一套标准化的**PaaS 平台**。

### 核心痛点与解决方案
| 核心痛点 (Pain Points) | 解决方案 (Solution) | 关键指标提升 (KPIs) |
| :--- | :--- | :--- |
| **发布风险高** | 引入蓝绿/滚动发布策略，配合 Readiness 探针 | 故障率降低 **90%** |
| **环境配置漂移** | 使用 Helm + Kustomize 实现配置代码化 (IaC) | 环境一致性 **100%** |
| **资源利用率低** | 基于 HPA (CPU/Mem) 的弹性扩缩容 | 成本节约 **40%** |
| **安全漏洞频发** | 流水线集成 Trivy 镜像扫描与 Sonar 代码检测 | 漏洞修复时效 **<24h** |

---

## 🏗️ 2. 系统架构 (System Architecture)

### 2.1 逻辑架构图
采用分层架构设计，确保关注点分离：

```mermaid
graph TD
    User[外部流量] -->|HTTPS| LB[F5/MetalLB]
    LB -->|TCP| Ingress[Nginx Ingress]
    
    subgraph "应用服务层 (Service Mesh)"
        Ingress -->|Route| SvcA[交易服务]
        Ingress -->|Route| SvcB[库存服务]
        SvcA -.->|gRPC| SvcB
    end

    subgraph "基础设施层 (Infrastructure)"
        K8s[Kubernetes Cluster]
        Net[Calico CNI Network]
        Storage[NFS/Ceph StorageClass]
    end

    subgraph "治理控制面 (Control Plane)"
        Prometheus[监控告警]
        Fluentd[日志采集]
        Jenkins[CI/CD]
        Harbor[镜像仓库]
    end
    
    Prometheus -->|Pull| K8s
    Fluentd -->|Watch| K8s
    Jenkins -->|Deploy| K8s
## 🏗️ 2. 系统架构 (System Architecture)

本平台采用经典的**云原生分层治理架构**，自下而上实现了基础设施的可编程化、平台服务的标准化以及业务应用的敏捷化。

### 2.1 逻辑架构分层视图 (Layered Architecture)

我们设计了四层架构模型，确保关注点分离（Separation of Concerns）：

| 层级 (Layer) | 核心组件 (Components) | 职责描述 (Description) |
| :--- | :--- | :--- |
| **接入层 (Ingress)** | F5 / MetalLB, Nginx Ingress | 负责七层流量卸载、SSL 终结、路由转发及黑白名单控制。 |
| **服务层 (Service)** | Spring Boot, Istio Sidecar | 承载核心业务逻辑，通过 Sidecar 实现熔断、限流与全链路追踪。 |
| **平台层 (Platform)** | K8s API, Harbor, Jenkins | 提供调度编排、镜像分发、CI/CD 流水线及配置中心能力。 |
| **基础层 (Infra)** | Kubeadm, Calico, Ceph/NFS | 提供计算资源池化、扁平化容器网络及分布式存储能力。 |

### 2.2 流量治理与网络架构 (Traffic & Network)

网络是 K8s 的命脉。本方案摒弃了传统的 Overlay 隧道封装，采用 **Calico BGP** 模式实现极致性能。

```mermaid
graph TD
    subgraph "南北向流量 (North-South Traffic)"
        Client[外部用户] -->|HTTPS| SLB[负载均衡器]
        SLB -->|TCP 80/443| Ingress[Nginx Controller]
        Ingress -->|Route Rule| Svc[K8s Service]
        Svc -->|Endpoints| Pod[业务容器]
    end

    subgraph "东西向流量 (East-West Traffic)"
        PodA[订单服务] -.->|Direct Route| PodB[库存服务]
        PodB -.->|NetworkPolicy| PodC[数据库]
    end
