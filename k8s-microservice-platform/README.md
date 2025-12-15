# ☸️ Kubernetes Microservice Automation Platform
# 基于 K8s 的企业级微服务自动化运维平台

<div align="center">

![Kubernetes](https://img.shields.io/badge/Kubernetes-v1.26-326ce5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-2.3-d24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-24.0-2496ed?style=for-the-badge&logo=docker&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-2.7-6db33f?style=for-the-badge&logo=spring-boot&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-e6522c?style=for-the-badge&logo=prometheus&logoColor=white)
![ElasticSearch](https://img.shields.io/badge/Elastic_Stack-EFK-005571?style=for-the-badge&logo=elasticsearch&logoColor=white)

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

<p align="center">
  <strong>一套完整的云原生落地解决方案：从基础设施搭建到应用自动化交付</strong>
</p>

</div>

---

## 📖 项目背景 (Background)

本项目源于一次真实的企业级架构转型实战。团队面临的主要挑战是将遗留的 **Python 单体应用** 迁移至现代化的 **Spring Boot 微服务** 架构。

在此过程中，我们需要解决**“发布周期长(2周)”**、**“故障定位难”**、**“资源利用率低”**等核心痛点。通过引入 Kubernetes 和 DevSecOps 体系，我们构建了这套集**自动化部署**、**弹性伸缩**、**全链路监控**于一体的运维平台。

---

## 🏗️ 系统架构 (Architecture)

### 🔄 CI/CD 流水线逻辑
本项目采用了经典的 **Jenkins + Helm + Kubernetes** 交付模式，结合 **GitLab Webhook** 实现代码提交即发布的自动化流程。

```mermaid
graph LR
    subgraph "DevSecOps Pipeline"
    Dev[开发者] -->|Git Push| GitLab
    GitLab -->|Webhook| Jenkins
    Jenkins -->|Maven Build| Jar[Artifact]
    Jenkins -->|SonarQube| CodeScan[代码扫描]
    Jenkins -->|Docker Build| Image[镜像]
    Image -->|Push| Harbor[私有仓库]
    end

    subgraph "Kubernetes Cluster"
    Jenkins -->|Helm Upgrade| K8sMaster
    K8sMaster -->|Schedule| WorkerNodes
    WorkerNodes -->|Pull Image| Harbor
    end
    
    subgraph "Observability"
    Prometheus -->|Pull Metrics| WorkerNodes
    Fluentd -->|Collect Logs| WorkerNodes
    Grafana -->|Visualize| Prometheus
    Kibana -->|Search| Elasticsearch
    end
