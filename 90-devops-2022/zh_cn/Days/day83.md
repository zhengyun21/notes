---
title: '#90DaysOfDevOps - 数据可视化 - Grafana - 第83天'
published: false
description: 90DaysOfDevOps - 数据可视化 - Grafana
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048767
---
## 数据可视化 - Grafana

在本节中，我们大量使用了 Kibana。但我们也需要花些时间来介绍 Grafana。不过，它们并不相同，也并不完全是竞争关系。

Kibana 的核心功能是数据查询和分析。通过各种方法，用户可以搜索 Elasticsearch 中索引的数据，查找特定事件或数据中的特定字符串，以进行根因分析和诊断。基于这些查询，用户可以使用 Kibana 的可视化功能，通过图表、表格、地理地图和其他类型的可视化方式以多种不同方式展示数据。

Grafana 实际上最初是作为 Kibana 的一个分支开始的，Grafana 的目标是提供对指标（即监控）的支持，而当时 Kibana 并不提供这一点。

Grafana 是一个免费且开源的数据可视化工具。我们在实际环境中经常将 Prometheus 和 Grafana 一起使用，但也可能看到 Grafana 与 Elasticsearch 和 Graphite 搭配使用。

这两个工具之间的关键区别是日志（Logging）与监控（Monitoring）。我们在本节开始时用 Nagios 介绍了监控，然后进入了 Prometheus，之后才进入日志部分，涵盖了 ELK 和 EFK Stack。

Grafana 专注于分析和可视化指标，如系统 CPU、内存、磁盘和 I/O 利用率。该平台不支持全文数据查询。Kibana 运行在 Elasticsearch 之上，主要用于分析日志消息。

正如我们已经发现的，Kibana 非常容易部署，而且可以选择部署位置，Grafana 也是如此。

两者都支持在 Linux、Mac、Windows、Docker 上安装或从源代码构建。

毫无疑问还有其他工具，但 Grafana 是我见过的跨越虚拟、云和云原生平台的工具，因此我想在本节中介绍它。

### Prometheus Operator + Grafana 部署

我们在本节中已经介绍过 Prometheus，但由于这两者经常搭配使用，我想搭建一个环境，让我们至少能看到可以在可视化中展示哪些指标。我们知道监控环境很重要，但仅在 Prometheus 或任何指标工具中浏览这些指标将是繁琐的，而且无法扩展。这就是 Grafana 的用武之地，它为我们提供了对 Prometheus 数据库中收集和存储的指标的交互式可视化。

通过这种可视化，我们可以为环境创建自定义图表、图形和告警。在本演练中，我们将使用 minikube 集群。

我们首先将其克隆到本地系统。使用 `git clone https://github.com/prometheus-operator/kube-prometheus.git` 和 `cd kube-prometheus`

![](Images/Day83_Monitoring1.png)

首先是在我们的 minikube 集群中创建命名空间：`kubectl create -f manifests/setup`。如果你之前没有跟随前面的章节，我们可以在这里使用 `minikube start` 来启动一个新集群。

![](Images/Day83_Monitoring2.png)

接下来，我们将使用 `kubectl create -f manifests/` 命令部署演示所需的一切，如你所见，这将在我们的集群中部署大量不同的资源。

![](Images/Day83_Monitoring3.png)

然后我们需要等待 Pod 启动并进入运行状态，我们可以使用 `kubectl get pods -n monitoring -w` 命令来关注 Pod 的状态。

![](Images/Day83_Monitoring4.png)

当一切运行正常时，我们可以使用 `kubectl get pods -n monitoring` 命令检查所有 Pod 是否处于运行和健康状态。

![](Images/Day83_Monitoring5.png)

通过部署，我们部署了一些稍后在演示中会用到的服务，你可以使用 `kubectl get svc -n monitoring` 命令查看这些服务。

![](Images/Day83_Monitoring6.png)

最后，让我们使用 `kubectl get all -n monitoring` 命令检查我们新的 monitoring 命名空间中部署的所有资源。

![](Images/Day83_Monitoring7.png)

打开一个新的终端，我们现在准备好访问我们的 Grafana 工具并开始收集和可视化一些指标了，要使用的命令是 `kubectl --namespace monitoring port-forward svc/grafana 3000`

![](Images/Day83_Monitoring8.png)

打开浏览器并导航到 http://localhost:3000，系统会提示你输入用户名和密码。

![](Images/Day83_Monitoring9.png)

默认的用户名和密码是：
```
Username: admin
Password: admin
```

不过，在首次登录时，系统会要求你设置一个新密码。你将看到的初始屏幕或主页会提供一些探索区域以及一些有用的资源，帮助你快速了解 Grafana 及其功能。注意 "Add your first data source" 和 "create your first dashboard" 小组件，我们稍后会用到它们。

![](Images/Day83_Monitoring10.png)

你会发现 Grafana 数据源中已经添加了一个 Prometheus 数据源，但由于我们使用的是 minikube，我们还需要端口转发 Prometheus，使其在我们的本地主机上可用。打开一个新终端，我们可以运行以下命令：`kubectl --namespace monitoring port-forward svc/prometheus-k8s 9090`。如果在 Grafana 主页上，我们现在进入 "Add your first data source" 小组件，从这里我们将选择 Prometheus。

![](Images/Day83_Monitoring11.png)

对于我们的新数据源，我们可以使用地址 http://localhost:9090，并且我们还需要将下拉菜单更改为 browser，如下所示。

![](Images/Day83_Monitoring12.png)

在页面底部，我们现在可以点击 save and test。如果 Prometheus 的端口转发正常工作，这应该会给你下面看到的结果。

![](Images/Day83_Monitoring13.png)

返回主页，找到 "Create your first dashboard" 选项，选择 "Add a new panel"

![](Images/Day83_Monitoring14.png)

你会看到，我们已经在从 Grafana 数据源收集数据，但我们希望从我们的 Prometheus 数据源收集指标，选择数据源下拉菜单并选择我们新创建的 "Prometheus-1"

![](Images/Day83_Monitoring15.png)

如果你选择 Metrics 浏览器，你会看到一长串从 Prometheus 收集的指标，这些指标与我们的 minikube 集群相关。

![](Images/Day83_Monitoring16.png)

出于演示目的，我将找到一个能给我们一些关于系统资源输出的指标，`cluster:node_cpu:ratio{}` 给了我们集群中节点的一些详细信息，并证明这个集成是有效的。

![](Images/Day83_Monitoring17.png)

当你对此可视化感到满意时，你可以点击右上角的 apply 按钮，然后会将此图表添加到你的仪表板中。显然，你可以继续添加其他图形和图表，以提供你需要的可视化效果。

![](Images/Day83_Monitoring18.png)

然而，我们可以利用成千上万个以前创建的仪表板，这样我们就不需要重复造轮子了。

![](Images/Day83_Monitoring19.png)

如果我们搜索 Kubernetes，我们会看到一长串预构建的仪表板供我们选择。

![](Images/Day83_Monitoring20.png)

我们选择了 Kubernetes API Server 仪表板，并将数据源更改为我们新添加的 Prometheus-1 数据源，然后我们可以看到下面显示的一些指标。

![](Images/Day83_Monitoring21.png)

### 告警

你还可以利用我们部署的 alertmanager 向 Slack 或其他集成发送告警，为此，你需要使用以下详细信息端口转发 alertmanager 服务。

`kubectl --namespace monitoring port-forward svc/alertmanager-main 9093`
http://localhost:9093

这总结了我们关于可观测性的一切内容。我个人发现本节突出了这个话题的广度，但同样重要的是，这对我们的角色来说是多么重要，无论是指标、日志还是追踪，随着我们在其他章节中已经涵盖的所有自动化，环境可能发生如此剧烈的变化，我们需要对未来的广泛环境有很好的了解。

接下来，我们将研究数据管理，以及 DevOps 原则在数据管理方面也需要被考虑。

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

[第八十四天](day84.md)见。
