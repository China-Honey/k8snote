[TOC]

# 一、K8S监控指标

#### Kubernetes本身监控

- Node资源利用率
- Node数量
- Pods数量（Node）
- 资源对象状态

#### Pod监控

- Pod数量（项目）
- 容器资源利用率
- 应用程序
- K8S监控指标





# 二、Prometheus是什么

​        Prometheus（普罗米修斯）是一个最初在SoundCloud上构建的监控系统。自2012年成为社区开源项目，拥有非常活跃的开发人员和用户社区。为强调开源及独立维护，Prometheus于2016年加入云原生云计算基金会（CNCF），成为继Kubernetes之后的第二个托管项目。
https://prometheus.io
https://github.com/prometheus



### Prometheus 特点：

- 多维数据模型：由度量名称和键值对标识的时间序列数据
- PromSQL：一种灵活的查询语言，可以利用多维数据完成复杂的查询
- 不依赖分布式存储，单个服务器节点可直接工作
- 基于HTTP的pull方式采集时间序列数据
- 推送时间序列数据通过PushGateway组件支持
- 通过服务发现或静态配置发现目标
- 多种图形模式及仪表盘支持（grafana）

# 三、Prometheus组件及架构

![1568272798482](assets\1568272798482.png)

- Prometheus Server：收集指标和存储时间序列数据，并提供查询接口
- ClientLibrary：客户端库
- Push Gateway：短期存储指标数据。主要用于临时性的任务
- Exporters：采集已有的第三方服务监控指标并暴露metrics
- Alertmanager：告警
- Web UI：简单的Web控制台







# 四、Prometheus数据模型

​       Prometheus将所有数据存储为时间序列；具有相同度量名称以及标签属于同一个指标。
每个时间序列都由度量标准名称和一组键值对（也成为标签）唯一标识。

### 时间序列格式：

```
<metric name>{<label name>=<label value>, ...}
示例：api_http_requests_total{method="POST", handler="/messages"}
```

实例：可以抓取的目标称为实例（Instances）
作业：具有相同目标的实例集合称为作业（Job）

```
scrape_configs:
  - job_name: 'prometheus'
static_configs:
  - targets: ['localhost:9090']
  - job_name: 'node'
static_configs:
  - targets: ['192.168.1.10:9090']
```



# 五、Prometheus监控K8S架构

| 监控指标    | 具体实现           | 举例                |
| ----------- | ------------------ | ------------------- |
| Pod性能     | cAdvisor           | 容器CPU，内存利用率 |
| Node性能    | node-exporter      | 节点CPU，内存利用率 |
| K8S资源对象 | kube-state-metrics |                     |

服务发现：
https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config



![1568272921837](assets\1568272921837.png)











# 六、在K8S中部署Prometheus

https://github.com/kubernetes/kubernetes/tree/master/cluster/addons/prometheus



# 七、监控K8S集群Pod、Node

### Pod

kubelet的节点使用cAdvisor提供的metrics接口获取该节点所有容器相关的性能指标数据。
暴露接口地址：
https://NodeIP:10255/metrics/cadvisor
https://NodeIP:10250/metrics/cadvisor



### Node

node_exporter：用于*NIX系统监控，使用Go语言编写的收集器。

使用文档：https://prometheus.io/docs/guides/node-exporter/
GitHub：https://github.com/prometheus/node_exporter
exporter列表：https://prometheus.io/docs/instrumenting/exporters/

# 八、在K8S中部署Grafana与可视化

Grafana是一个开源的度量分析和可视化系统。

https://grafana.com/grafana/download

#### 推荐模板：

集群资源监控：3119
资源状态监控：6417
Node监控：9276

# 九、监控K8S集群中Pod与资源对象

kube-state-metrics采集了k8s中各种资源对象的状态信息：
kube_daemonset_*
kube_deployment_*
kube_job_*
kube_namespace_*
kube_node_*
kube_persistentvolumeclaim_*
kube_pod_container_*
kube_pod_*
kube_replicaset_*
kube_service_*
kube_statefulset_*

https://github.com/kubernetes/kube-state-metrics





# 十、在K8S中部署Alertmanager



![1568273961364](assets\1568273961364.png)

1. 部署Alertmanager
2. 配置Prometheus与Alertmanager通信
3. 配置告警
    - prometheus指定rules目录
    - configmap存储告警规则
    - configmap挂载到容器rules目录
    - 增加alertmanager告警配置





# 十一、告警规则与告警通知

