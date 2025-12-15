# GitLab Webhook 配置指南

## 触发条件
- **Event**: Push Events
- **Branch**: main / master

## 配置 URL
```
https://jenkins.company.com/project/demo-service/build\?token\=YOUR_SECRET_TOKEN
```

## 安全设置
- 启用 SSL 验证
- 配置 Secret Token 防止恶意触发
