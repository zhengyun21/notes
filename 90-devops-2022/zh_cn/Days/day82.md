---
title: '#90DaysOfDevOps - EFK Stack - 第82天'
published: false
description: 90DaysOfDevOps - EFK Stack
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049059
---
### EFK Stack

在上一节中，我们谈到了 ELK Stack，它使用 Logstash 作为栈中的日志收集器。在 EFK Stack 中，我们将 Logstash 替换为 Fluentd 或 Fluent Bit。

我们在本节中的目标是使用 EFK 监控我们的 Kubernetes 日志。

### EFK 概述

我们将把以下内容部署到我们的 Kubernetes 集群中。

![](Images/Day82_Monitoring1.png)

EFK Stack 是三个软件的集合，包括：

- Elasticsearch：用于存储数据的 NoSQL 数据库，并提供搜索和查询日志的接口。

- Fluentd：Fluentd 是一个用于统一日志层的开源数据收集器。Fluentd 让你能够统一数据收集和消费，以便更好地使用和理解数据。

- Kibana：用于管理和统计日志的界面。负责从 Elasticsearch 读取信息。

### 在 Minikube 上部署 EFK

我们将使用我们可靠的 minikube 集群来部署 EFK Stack。让我们在系统上使用 `minikube start` 启动一个集群。我使用的是启用了 WSL2 的 Windows 操作系统。

![](Images/Day82_Monitoring2.png)

我创建了 [efk-stack.yaml](Days/Monitoring/../../Monitoring/EFK%20Stack/efk-stack.yaml)，其中包含我们需要将 EFK Stack 部署到集群中的所有内容。使用 `kubectl create -f efk-stack.yaml` 命令，我们可以看到所有内容正在被部署。

![](Images/Day82_Monitoring3.png)

根据你的系统情况，以及你是否已经运行过此操作并已拉取镜像，你现在应该观察 Pod 进入就绪状态，然后才能继续。你可以使用以下命令检查进度：`kubectl get pods -n kube-logging -w`。这可能需要几分钟。

![](Images/Day82_Monitoring4.png)

上面的命令让我们持续关注情况，但我更喜欢通过运行以下 `kubectl get pods -n kube-logging` 命令来确认一切正常，确保所有 Pod 都已启动并运行。

![](Images/Day82_Monitoring5.png)

一旦我们所有的 Pod 都启动并运行，此时我们应该看到：
- 3 个与 ElasticSearch 关联的 Pod
- 1 个与 Fluentd 关联的 Pod
- 1 个与 Kibana 关联的 Pod

我们还可以使用 `kubectl get all -n kube-logging` 来显示我们命名空间中的所有资源。如前所述，Fluentd 以 DaemonSet 的形式部署，Kibana 以 Deployment 的形式部署，Elasticsearch 以 StatefulSet 的形式部署。

![](Images/Day82_Monitoring6.png)

现在所有 Pod 都已启动并运行，我们可以在新的终端中发出端口转发命令，以便访问我们的 Kibana 仪表板。请注意，你的 Pod 名称将与这里看到的命令不同。`kubectl port-forward kibana-84cf7f59c-v2l8v 5601:5601 -n kube-logging`

![](Images/Day82_Monitoring7.png)

我们现在可以打开浏览器并导航到这个地址：http://localhost:5601。你会看到下面显示的屏幕，或者你可能会看到一个示例数据屏幕，或者继续自行配置。无论如何，一定要看看那些测试数据，这是我们在之前学习 ELK Stack 时涵盖的内容。

![](Images/Day82_Monitoring8.png)

接下来，我们需要点击左侧菜单的 "discover" 标签，并将 "*" 添加到我们的索引模式中。点击 "Next step" 继续下一步。

![](Images/Day82_Monitoring9.png)

在步骤 2/2 中，我们将从下拉菜单中选择 @timestamp 选项，因为这将按时间过滤我们的数据。当你点击创建模式时，可能需要几秒钟才能完成。

![](Images/Day82_Monitoring10.png)

如果现在我们在几秒钟后回到 "discover" 标签，你应该开始看到来自 Kubernetes 集群的数据流入。

![](Images/Day82_Monitoring11.png)

现在我们的 EFK Stack 已经启动并运行，我们通过 Fluentd 从 Kubernetes 集群收集日志，我们还可以看看其他可以选择的源。如果你通过点击左上角的 Kibana 徽标返回主页，你会看到与我们首次登录时相同的页面。

我们有能力从其他插件或源添加 APM、日志数据、指标数据和安全事件。

![](Images/Day82_Monitoring12.png)

如果我们选择 "Add log data"，那么我们可以看到下面有很多选择，可以选择从哪里获取日志，你可以看到 Logstash 也在其中提到，它是 ELK Stack 的一部分。

![](Images/Day82_Monitoring13.png)

在指标数据下，你会发现可以添加 Prometheus 和许多其他服务的源。

### APM（应用性能监控）

还有收集 APM（Application Performance Monitoring，应用性能监控）的选项，它从应用内部收集深入的性能指标和错误。它允许你实时监控数千个应用的性能。

我不会在这里深入探讨 APM，但你可以在 [Elastic 官网](https://www.elastic.co/observability/application-performance-monitoring) 了解更多。


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

[第八十三天](day83.md)见。
