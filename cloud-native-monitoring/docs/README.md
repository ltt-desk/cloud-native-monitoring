# 云原生监控告警平台

![GitHub stars](https://img.shields.io/github/stars/your-username/cloud-native-monitoring)
![GitHub license](https://img.shields.io/github/license/your-username/cloud-native-monitoring)
![Kubernetes Version](https://img.shields.io/badge/kubernetes-1.21%2B-blue)

基于Kubernetes、Prometheus、Grafana、Alertmanager和EFK Stack构建的企业级监控告警解决方案，提供全面的集群和应用监控能力。

## 项目介绍

该平台旨在为云原生环境提供一站式监控告警解决方案，主要特点包括：

- **全面监控**：实时采集Kubernetes集群CPU/内存等关键指标及应用日志
- **智能告警**：基于自定义规则自动触发告警，支持邮件通知
- **可视化分析**：通过直观的仪表盘展示系统运行状态和性能趋势
- **日志管理**：集中收集、存储和分析应用日志，支持快速检索
- **多环境支持**：通过Helm Chart实现开发/生产环境一键部署
- **企业级实践**：已在多个项目中验证，故障定位时间从2小时缩短至15分钟

## 技术栈

- **容器编排**：Kubernetes 1.21+
- **指标监控**：Prometheus
- **可视化**：Grafana
- **告警管理**：Alertmanager
- **日志系统**：EFK Stack (Elasticsearch, Fluentd, Kibana)
- **部署工具**：Helm 3
- **脚本语言**：Bash

## 架构设计

![架构图](docs/architecture.png)

### 核心组件

1. **Prometheus**：负责从Kubernetes集群和应用中采集时序指标数据
2. **Grafana**：提供丰富的可视化仪表盘，展示监控数据
3. **Alertmanager**：处理Prometheus产生的告警，支持分组、抑制和通知
4. **EFK Stack**：
   - **Elasticsearch**：分布式存储和索引日志数据
   - **Fluentd**：收集和处理容器日志
   - **Kibana**：提供日志查询、分析和可视化界面

## 快速开始

### 环境要求

- Kubernetes集群 (1.21+)，至少3个节点
- 每个节点至少2核CPU和4GB内存
- Helm 3.0+
- kubectl 客户端工具
- 具备集群管理员权限

### 部署步骤

1. **克隆仓库**
git clone https://github.com/your-username/cloud-native-monitoring.git
cd cloud-native-monitoring
2. **部署监控平台**
# 部署到生产环境（默认）
./scripts/deploy.sh

# 部署到开发环境
./scripts/deploy.sh -e development
3. **验证部署**
# 检查部署状态
./scripts/check-status.sh
4. **访问各组件**
# Grafana (默认用户名: admin，密码见部署输出)
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80

# Kibana
kubectl port-forward -n logging svc/efk-kibana 5601:5601

# Prometheus
kubectl port-forward -n monitoring svc/monitoring-prometheus 9090:80

# Alertmanager
kubectl port-forward -n monitoring svc/monitoring-alertmanager 9093:80
然后在浏览器中访问对应的地址：
- Grafana: http://localhost:3000
- Kibana: http://localhost:5601
- Prometheus: http://localhost:9090
- Alertmanager: http://localhost:9093

## 使用指南

### 配置告警通知

1. 修改Alertmanager配置：
# 编辑配置文件
vi configs/alertmanager/alertmanager.yml

# 重新部署使配置生效
helm upgrade monitoring ./helm/monitoring -n monitoring
2. 配置项说明：
   - `smtp_smarthost`：SMTP服务器地址和端口
   - `smtp_from`：发送告警的邮箱地址
   - `smtp_auth_username`：SMTP认证用户名
   - `to`：接收告警的邮箱地址

### 导入Grafana仪表盘

1. 登录Grafana后，进入"Dashboards" -> "Import"
2. 输入仪表盘ID或上传`configs/grafana/dashboards/`目录下的JSON文件
3. 推荐的仪表盘：
   - K8s集群监控：`k8s-cluster.json`
   - Spring Boot应用监控：`spring-boot-app.json`

### 日志查询

1. 访问Kibana后，首次使用需创建索引模式：
   - 进入"Management" -> "Stack Management" -> "Index Patterns"
   - 创建索引模式：`logs-*`
   - 选择时间字段：`@timestamp`

2. 在"Discover"页面可以：
   - 通过时间范围筛选日志
   - 使用KQL (Kibana Query Language) 进行高级查询
   - 保存常用查询条件

## 自定义配置

### 修改监控规则

1. 编辑Prometheus规则文件：
# CPU使用率规则
vi configs/prometheus/rules/cpu-usage.rules.yml

# 内存使用率规则
vi configs/prometheus/rules/memory-usage.rules.yml
2. 重新加载配置：
kubectl delete pod -l app.kubernetes.io/component=prometheus -n monitoring
### 调整资源配置

根据实际环境需求，可以调整各组件的资源配置：
# 生产环境配置
vi helm/monitoring/values.yaml
vi helm/efk/values.yaml

# 开发环境配置
vi helm/monitoring/values-dev.yaml
vi helm/efk/values-dev.yaml
## 卸载
./scripts/uninstall.sh

# 如需彻底清除所有数据（包括PVC），运行卸载脚本后选择删除命名空间
## 文档

- [云原生监控实践指南](docs/云原生监控实践指南.md)
- [架构设计说明](docs/architecture.md)
- [常见问题排查](docs/troubleshooting.md)

## 贡献

欢迎提交issue和PR来改进这个项目。提交代码前请确保通过了基本的功能测试。

## 许可证

本项目采用MIT许可证 - 详见LICENSE文件
