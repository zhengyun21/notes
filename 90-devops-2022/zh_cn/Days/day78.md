---
title: '#90DaysOfDevOps - 监控工具动手实践 - 第78天'
published: false
description: 90DaysOfDevOps - 监控工具动手实践
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049056
---
## 监控工具动手实践

在上一个 session 中，我谈到了监控的全景图，并研究了 Nagios，这样做有两个原因。首先，这是我多年来经常听到的一款软件，所以我想更多地了解它的功能。

今天我将深入介绍 Prometheus，我在云原生（Cloud-Native）领域越来越多地看到 Prometheus，但它也可以用于管理 Kubernetes 等之外的物理资源。

### Prometheus - 几乎能监控一切

首先，Prometheus 是开源的，可以帮助你监控基于容器和微服务的系统，以及物理、虚拟和其他服务。Prometheus 背后有一个庞大的社区。

Prometheus 拥有大量的[集成和 exporters](https://prometheus.io/docs/instrumenting/exporters/)，关键是将现有的指标导出为 prometheus 指标。除此之外，它还支持多种编程语言。

Pull 方式 - 如果你要与数千个微服务或系统和服务进行通信，push（推送）方法通常是服务向监控系统推送数据。这带来了一些挑战，如网络洪泛、高 CPU 以及单点故障。而 Pull（拉取）方式给了我们更好的体验，Prometheus 会从每个服务的指标端点（metrics endpoint）拉取数据。

我们再次看到 Prometheus 使用 YAML 进行配置。

![](https://cdn.jsdelivr.net/gh/prometheus/prometheus@c34257d069c630685da35bcef084632ffd5d6209/documentation/images/architecture.svg)

稍后你会看到部署到 Kubernetes 中时这是什么样子的，特别是我们有 **PushGateway**，它从我们的 jobs/exporters 拉取指标。

我们有 **AlertManager**，它推送警报，在这里我们可以集成到外部服务，如 email、slack 和其他工具。

然后是 Prometheus server（服务器），它管理从 PushGateway 拉取这些指标，然后将那些推送警报发送到 AlertManager。Prometheus server 还将数据存储在本地磁盘上，不过也可以利用远程存储解决方案。

我们还有 PromQL，这是与指标交互所使用的语言，稍后可以在 Prometheus Web UI 中看到，但在本节的后面你也会看到它如何被用于 Grafana 等数据可视化工具中。

### 部署 Prometheus 的方式

有多种安装 Prometheus 的方式，[下载页面](https://prometheus.io/download/)中也有可用的 Docker 镜像。

`docker run --name prometheus -d -p 127.0.0.1:9090:9090 prom/prometheus`

但我们将集中精力部署到 Kubernetes。这也有些选项。

- 创建配置 YAML 文件
- 使用 Operator（管理所有 prometheus 组件）
- 使用 helm chart 部署 operator

### 部署到 Kubernetes

我们将再次在本地使用 minikube 集群进行这次快速简单的安装。与之前接触 minikube 时一样，我们将使用 helm 部署 Prometheus helm chart。

`helm repo add prometheus-community https://prometheus-community.github.io/helm-charts`

![](Images/Day78_Monitoring1.png)

如上图所示，我们还运行了 helm repo update，现在我们已经准备好使用 `helm install stable prometheus-community/prometheus` 命令将 Prometheus 部署到我们的 minikube 环境中。

![](Images/Day78_Monitoring2.png)

几分钟后，你会看到一些新的 pod 出现，对于这个演示，我已经将它们部署到了 default 命名空间，通常我会将它们推送到自己的命名空间。

![](Images/Day78_Monitoring3.png)

一旦所有 pod 都在运行，我们还可以查看 Prometheus 部署的所有方面。

![](Images/Day78_Monitoring4.png)

现在，为了访问 Prometheus Server UI，我们可以使用以下命令进行端口转发。

```
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090
```

当我们第一次用浏览器打开 http://localhost:9090 时，会看到下面这个非常空白的界面。

![](Images/Day78_Monitoring5.png)

因为我们已经部署到了 Kubernetes 集群，所以我们将自动从 Kubernetes API 收集指标，因此我们可以使用一些 PromQL 来至少确保我们正在捕获指标 `container_cpu_usage_seconds_total`

![](Images/Day78_Monitoring6.png)

关于学习 PromQL 并将其付诸实践，正如我之前提到的，获取指标很棒，监控也很棒，但你必须知道你在监控什么、为什么监控，以及你没有监控什么、为什么不监控！

我想回到 Prometheus，但现在我认为我们需要思考一下日志管理（Log Management）和数据可视化，然后再回到 Prometheus。

## 资源

- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)
- [Promql cheat sheet with examples](https://www.containiq.com/post/promql-cheat-sheet-with-examples)

[第79天](day79.md)见。
