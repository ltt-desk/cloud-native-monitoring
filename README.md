# 云原生监控告警平台

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/ltt-desk/cloud-native-monitor.svg)](https://github.com/ltt-desk/cloud-native-monitor/stargazers)

一款基于云原生技术栈的分布式监控告警平台，支持 Kubernetes 集群、容器、应用服务的全方位监控与智能告警，帮助运维团队实时掌握系统状态。


## 🌟 项目概述

随着微服务和容器化的普及，传统监控工具难以满足分布式系统的复杂性。本项目基于 Prometheus、Grafana、AlertManager 等云原生生态组件，构建了一套可扩展、高可用的监控告警解决方案，具备以下优势：

- 全栈监控：从基础设施（服务器、网络）到应用层（容器、API、数据库）的全链路指标采集
- 实时告警：支持多级别告警策略（P0-P3）和多渠道通知（邮件、钉钉、Slack）
- 可视化 dashboards：预置常用监控面板，支持自定义指标展示
- 云原生适配：原生支持 Kubernetes 环境，自动发现集群内资源


## 🚀 核心功能

| 功能模块       | 说明                                                                 |
|----------------|----------------------------------------------------------------------|
| 指标采集       | 基于 Prometheus 采集主机、容器、应用指标，支持自定义 Exporter          |
| 数据存储       | 时序数据库存储，支持数据分片和长期归档                               |
| 可视化展示     | Grafana 预置面板：集群健康度、资源使用率、应用响应时间等             |
| 告警管理       | 支持阈值告警、趋势告警、异常检测，可配置告警升级策略                 |
| 通知集成       | 对接企业微信、钉钉、邮件、PagerDuty 等通知渠道                       |
| 日志关联       | 可选集成 ELK 栈，实现指标与日志的联动分析（需额外配置）              |


## 🔧 快速开始

### 环境要求

- Kubernetes 1.20+ 集群（推荐）或 Docker Compose 环境
- Helm 3.0+（用于 Kubernetes 部署）
- 至少 2GB 内存、2 核 CPU（测试环境）


### 部署方式

#### 1. Kubernetes 部署（推荐）
# 添加 Helm 仓库
helm repo add cloud-monitor https://ltt-desk.github.io/cloud-native-monitor/charts

# 安装监控平台（默认部署 Prometheus + Grafana + AlertManager）
helm install cloud-monitor cloud-monitor/cloud-native-monitor \
  --namespace monitor-system \
  --create-namespace \
  --set alertmanager.enabled=true \
  --set grafana.adminPassword=your-password
#### 2. Docker Compose 部署（本地测试）
# 克隆仓库
git clone https://github.com/ltt-desk/cloud-native-monitor.git
cd cloud-native-monitor

# 启动服务
docker-compose up -d

# 查看状态
docker-compose ps

### 访问系统

- **Grafana 控制台**：`http://<你的IP>:3000`（默认账号密码：admin/your-password）
- **Prometheus 界面**：`http://<你的IP>:9090`
- **AlertManager 界面**：`http://<你的IP>:9093`


## 📊 监控指标说明

系统默认采集以下几类核心指标，可在 Grafana 中查看详细面板：

| 监控对象       | 关键指标示例                          | 告警阈值建议               |
|----------------|---------------------------------------|----------------------------|
| 主机           | CPU 使用率、内存使用率、磁盘 IO       | CPU > 80% 持续 5 分钟      |
| Kubernetes 节点 | 节点就绪状态、Pod 运行状态            | 节点 NotReady 持续 3 分钟  |
| 容器           | 容器重启次数、CPU/内存限制使用率      | 重启次数 > 5 次/小时       |
| 应用服务       | 接口响应时间、错误率、QPS             | 错误率 > 1% 持续 1 分钟    |


## ⚙️ 告警配置

1. 在 `config/alert-rules/` 目录下定义告警规则（PromQL 语法）：
   ```yaml
   # 示例：高CPU使用率告警
   groups:
   - name: host-alerts
     rules:
     - alert: HighCpuUsage
       expr: 100 - (avg(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
       for: 5m
       labels:
         severity: P1
       annotations:
         summary: "主机 {{ $labels.instance }} CPU 使用率过高"
         description: "当前使用率: {{ $value | humanizePercentage }}"
   ```

2. 配置通知渠道（`config/alertmanager.yml`）：
   ```yaml
   route:
     receiver: 'dingtalk'
   receivers:
   - name: 'dingtalk'
     webhook_configs:
     - url: 'http://dingtalk-webhook:8080/send'
   ```


## 🏗️ 架构设计

![架构图](https://github.com/ltt-desk/cloud-native-monitoring/blob/main/cloud-native-monitoring/docs/%E6%9E%B6%E6%9E%84%E5%9B%BE%E8%AE%BE%E8%AE%A1.md))

- **采集层**：Node Exporter（主机指标）、cAdvisor（容器指标）、应用自定义 Exporter
- **存储层**：Prometheus 时序数据库，支持远程存储（如 Thanos）
- **分析层**：PromQL 指标查询、AlertManager 告警规则评估
- **展示层**：Grafana 可视化面板
- **通知层**：Webhook 集成第三方通知服务


## 🤝 贡献指南

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feature/amazing-feature`
3. 提交修改：`git commit -m 'Add some amazing feature'`
4. 推送到分支：`git push origin feature/amazing-feature`
5. 打开 Pull Request


## 📄 许可证

本项目基于 MIT 许可证开源，详情参见 [LICENSE](LICENSE) 文件。


## 📞 联系我们

- 项目维护者：ltt-desk（<2803433185@qq.com>）
- 问题反馈：[GitHub Issues](https://github.com/ltt-desk/cloud-native-monitor/issues)
