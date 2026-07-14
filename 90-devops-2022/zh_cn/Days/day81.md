---
title: '#90DaysOfDevOps - Fluentd & FluentBit - 第81天'
published: false
description: 90DaysOfDevOps - Fluentd & FluentBit
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048716
---
## Fluentd & FluentBit

作为可观测性部分的另一个数据收集器，我想探索一下 [Fluentd](https://docs.fluentd.org/)。它是一个开源的统一日志层。

Fluentd 有四个关键特性，使其适合构建干净、可靠的日志管道：

**使用 JSON 的统一日志记录**：Fluentd 尽可能将数据结构化为 JSON。这使得 Fluentd 能够统一处理日志数据的各个方面：跨多个来源和目标收集、过滤、缓冲和输出日志。由于 JSON 具有足够的结构，可以在不强制固定模式的情况下被访问，因此下游数据处理变得更加容易。

**可插拔架构**：Fluentd 拥有灵活的插件系统，允许社区扩展其功能。超过 300 个社区贡献的插件将数十个数据源连接到数十个数据输出端，根据需要操作数据。通过使用插件，你可以立即更好地利用日志。

**最低资源需求**：数据收集器应该足够轻量，以便在繁忙的机器上舒适运行。Fluentd 由 C 和 Ruby 组合编写，只需要极少的系统资源。普通实例运行在 30-40MB 内存上，每秒可处理 13,000 个事件/核心。

**内置可靠性**：数据丢失绝不应该发生。Fluentd 支持基于内存和文件的缓冲，以防止节点间的数据丢失。Fluentd 还支持强大的故障转移，并可以设置为高可用。

[安装 Fluentd](https://docs.fluentd.org/quickstart#step-1-installing-fluentd)

### 应用如何记录日志？

- 写入文件。`.log` 文件（没有工具的话难以分析，且难以扩展）
- 直接记录到数据库（每个应用必须配置正确的格式）
- 第三方应用（NodeJS、NGINX、PostgreSQL）

这就是为什么我们需要一个统一的日志层。

Fluentd 允许处理上述三种日志数据类型，并使我们能够收集、处理和发送这些数据到目标端，例如将日志发送到 Elastic、MongoDB、Kafka 等数据库。

任何数据、任何数据源都可以发送到 Fluentd，然后 Fluentd 可以将其发送到任何目标。Fluentd 不绑定于任何特定的源或目标。

在研究 Fluentd 的过程中，我不断遇到 Fluent Bit 作为另一个选择，看起来如果你想在 Kubernetes 环境中部署日志工具，Fluent Bit 可以提供这种能力，尽管 Fluentd 也可以部署到容器和服务器上。

[Fluentd & Fluent Bit](https://docs.fluentbit.io/manual/about/fluentd-and-fluent-bit)

Fluentd 和 Fluent Bit 会使用输入插件将数据转换为 Fluent Bit 格式，然后我们使用输出插件将数据发送到目标端，例如 Elasticsearch。

我们还可以在配置之间使用标签（tags）和匹配（matches）。

我看不出有什么充分的理由使用 Fluentd，似乎 Fluent Bit 是入门的最佳方式。尽管它们可以在某些架构中一起使用。

### Kubernetes 中的 Fluent Bit

Fluent Bit 在 Kubernetes 中以 DaemonSet 的形式部署，这意味着它会在集群的每个节点上运行。每个节点上的 Fluent Bit Pod 会读取该节点上的每个容器并收集所有可用的日志。它还会从 Kubernetes API Server 收集元数据。

Kubernetes 注解可以在我们应用的配置 YAML 中使用。


首先，我们可以从 Fluent Helm 仓库部署。`helm repo add fluent https://fluent.github.io/helm-charts`，然后使用 `helm install fluent-bit fluent/fluent-bit` 命令安装。

![](Images/Day81_Monitoring1.png)

在我的集群中，我还在默认命名空间中运行着 Prometheus（用于测试目的），我们需要确保我们的 fluent-bit Pod 正在运行。我们可以使用 `kubectl get all | grep fluent` 来查看正在运行的 Pod、服务和前面提到的 DaemonSet。

![](Images/Day81_Monitoring2.png)

为了让 Fluent Bit 知道从哪里获取日志，我们有一个配置文件。在这个 Kubernetes 部署的 Fluent Bit 中，我们有一个 ConfigMap，类似于配置文件。

![](Images/Day81_Monitoring3.png)

该 ConfigMap 看起来像这样：

```
Name:         fluent-bit
Namespace:    default
Labels:       app.kubernetes.io/instance=fluent-bit
              app.kubernetes.io/managed-by=Helm
              app.kubernetes.io/name=fluent-bit
              app.kubernetes.io/version=1.8.14
              helm.sh/chart=fluent-bit-0.19.21
Annotations:  meta.helm.sh/release-name: fluent-bit
              meta.helm.sh/release-namespace: default

Data
====
custom_parsers.conf:
----
[PARSER]
    Name docker_no_time
    Format json
    Time_Keep Off
    Time_Key time
    Time_Format %Y-%m-%dT%H:%M:%S.%L

fluent-bit.conf:
----
[SERVICE]
    Daemon Off
    Flush 1
    Log_Level info
    Parsers_File parsers.conf
    Parsers_File custom_parsers.conf
    HTTP_Server On
    HTTP_Listen 0.0.0.0
    HTTP_Port 2020
    Health_Check On

[INPUT]
    Name tail
    Path /var/log/containers/*.log
    multiline.parser docker, cri
    Tag kube.*
    Mem_Buf_Limit 5MB
    Skip_Long_Lines On

[INPUT]
    Name systemd
    Tag host.*
    Systemd_Filter _SYSTEMD_UNIT=kubelet.service
    Read_From_Tail On

[FILTER]
    Name kubernetes
    Match kube.*
    Merge_Log On
    Keep_Log Off
    K8S-Logging.Parser On
    K8S-Logging.Exclude On

[OUTPUT]
    Name es
    Match kube.*
    Host elasticsearch-master
    Logstash_Format On
    Retry_Limit False

[OUTPUT]
    Name es
    Match host.*
    Host elasticsearch-master
    Logstash_Format On
    Logstash_Prefix node
    Retry_Limit False

Events:  <none>
```

我们现在可以将 Pod 端口转发到本地主机以确保连通性。首先使用 `kubectl get pods | grep fluent` 获取你的 Pod 名称，然后使用 `kubectl port-forward fluent-bit-8kvl4 2020:2020`，打开浏览器访问 http://localhost:2020/

![](Images/Day81_Monitoring4.png)

我还找到了一篇非常好的 Medium 文章，涵盖了更多关于 [Fluent Bit](https://medium.com/kubernetes-tutorials/exporting-kubernetes-logs-to-elasticsearch-using-fluent-bit-758e8de606af) 的内容。

## 相关资料

- [Understanding Logging: Containers & Microservices](https://www.youtube.com/watch?v=MMVdkzeQ848)
- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)
- [Promql cheat sheet with examples](https://www.containiq.com/post/promql-cheat-sheet-with-examples)
- [Log Management for DevOps | Manage application, server, and cloud logs with Site24x7](https://www.youtube.com/watch?v=J0csO_Shsj0)
- [Log Management what DevOps need to know](https://devops.com/log-management-what-devops-teams-need-to-know/)
- [What is ELK Stack?](https://www.youtube.com/watch?v=4X0WLg05ASw)
- [Fluentd simply explained](https://www.youtube.com/watch?v=5ofsNyHZwWE&t=14s)
- [ Fluent Bit explained | Fluent Bit vs Fluentd ](https://www.youtube.com/watch?v=B2IS-XS-cc0)


[第八十二天](day82.md)见。
