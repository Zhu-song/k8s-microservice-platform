# ☸️ Enterprise Cloud-Native Microservice Platform
# 企业级云原生微服务自动化运维平台

<div align="center">
  <a href="https://github.com/your-username/k8s-microservice-platform">
    <img src="https://kubernetes.io/images/wheel.svg" alt="Logo" width="100" height="100">
  </a>

  <h2 align="center">构建高可用 (99.99%)、分钟级发布、全链路监控的生产级基础设施</h2>

  <p align="center">
    基于 <strong>Kubernetes v1.26</strong> + <strong>Jenkins</strong> + <strong>Istio Ready</strong> 的架构转型实战
    <br />
    <a href="#-系统架构"><strong>探索架构图 »</strong></a>
    <br />
    <br />
    <a href="#-快速部署">快速部署</a>
    ·
    <a href="#-运维手册--故障排查">SRE 手册</a>
    ·
    <a href="#-性能基准测试">性能对比</a>
  </p>
</div>

<div align="center">

![CI/CD](https://img.shields.io/badge/CI%2FCD-Jenkins%20%2B%20GitLab-blue?style=for-the-badge&logo=jenkins)
![K8s](https://img.shields.io/badge/Orchestration-Kubernetes-326ce5?style=for-the-badge&logo=kubernetes)
![Spring](https://img.shields.io/badge/Microservice-Spring%20Boot-6db33f?style=for-the-badge&logo=spring-boot)
![Observability](https://img.shields.io/badge/Observability-Prometheus%20%26%20EFK-e6522c?style=for-the-badge&logo=prometheus)
![Network](https://img.shields.io/badge/Network-Calico%20BGP-f3552e?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

</div>

---

## 📖 项目综述 (Executive Summary)

本项目是针对某高并发业务场景进行的**架构重构实战**。我们将原有的 Python 单体应用（Monolithic）成功解耦并迁移至基于 **Spring Boot** 的微服务架构，并部署在自建的 **Kubernetes** 集群上。

平台不仅仅是技术的堆砌，更是一套经过**生产环境验证**的运维方法论，解决了旧架构中的以下核心痛点：
* ❌ **发布黑盒**：旧系统部署依赖人工脚本，回滚困难，发布周期长达 **2周**。
* ❌ **资源浪费**：无法根据流量自动伸缩，低峰期服务器资源闲置率高达 **70%**。
* ❌ **监控盲区**：缺乏应用内部视角，OOM 或线程池满导致服务假死时无法第一时间报警。

**✅ 核心成果**：构建了一套支持 **“代码提交即上线”** 的自动化流水线，将发布时间压缩至 **分钟级**，并将集群稳定性提升至 **99.95%**。

---

## 🏗️ 系统架构 (Architecture)

### 1. 顶层设计图 (High-Level Design)

```mermaid
graph TB
    subgraph "External Access"
        User[User Traffic] -->|HTTPS/443| LB[Load Balancer]
        LB -->|Route| Ingress[Nginx Ingress Controller]
    end

    subgraph "K8s Cluster (Production)"
        Ingress -->|Service Discovery| Svc[K8s Service]
        Svc -->|Load Balance| Pod1[Spring Boot App V1]
        Svc -->|Load Balance| Pod2[Spring Boot App V2]
        
        HPA[Horizontal Pod Autoscaler] -.->|Watch Metrics| MetricsServer
        MetricsServer -.->|Scale Out/In| Pod1
    end

    subgraph "DevSecOps Plane"
        Dev[Developer] -->|Git Push| GitLab
        GitLab -->|Webhook| Jenkins
        Jenkins -->|Unit Test & Build| CI_Worker
        CI_Worker -->|Push Image| Harbor
        Jenkins -->|Helm Upgrade| K8s_API
    end

    subgraph "Observability Plane"
        Prometheus -->|Scrape| Pod1
        Fluentd -->|Tail Logs| Pod1
        Grafana -->|Visualize| Prometheus
        Kibana -->|Analyze| ElasticSearch
    end
