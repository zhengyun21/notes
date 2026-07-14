---
title: '#90DaysOfDevOps - ELK Stack - 第80天'
published: false
description: 90DaysOfDevOps - ELK Stack
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048746
---
## ELK Stack

在本节中，我们将对我们提到的一些选项进行更多动手实践。

### ELK Stack

ELK Stack 是三个独立工具的组合：

- [Elasticsearch](https://www.elastic.co/what-is/elasticsearch) 是一个分布式、免费且开源的搜索和分析引擎，适用于所有类型的数据，包括文本、数值、地理空间、结构化和非结构化数据。

- [Logstash](https://www.elastic.co/logstash/) 是一个免费且开源的服务端数据处理管道，可以从多个来源摄取数据，对其进行转换，然后发送到你最喜欢的 "存储库"。

- [Kibana](https://www.elastic.co/kibana/) 是一个免费且开源的用户界面，可让你可视化 Elasticsearch 数据并导航 Elastic Stack。从跟踪查询负载到了解请求在应用中的流转方式，无所不能。

ELK Stack 让我们能够可靠且安全地从任何来源、以任何格式获取数据，然后实时搜索、分析和可视化这些数据。

除了上述组件之外，你还可能会看到 Beats，它们是安装在边缘主机上的轻量级代理，用于收集不同类型的数据并转发到栈中。


- 日志：需要分析的服务器日志被识别出来

- Logstash：收集日志和事件数据，甚至进行解析和转换

- Elasticsearch：来自 Logstash 的转换数据被存储、搜索和索引。

- Kibana 使用 Elasticsearch 数据库来探索、可视化和分享

![](https://www.guru99.com/images/tensorflow/082918_1504_ELKStackTut1.png)

[图片来自 Guru99](https://www.guru99.com/elk-stack-tutorial.html)

一篇很好的资源解释了这一点：[The Complete Guide to the ELK Stack](https://logz.io/learn/complete-guide-elk-stack/)

随着 Beats 的加入，ELK Stack 现在也被称为 Elastic Stack。

对于动手实践场景，你可以在多个地方部署 Elastic Stack，但我们将使用 Docker Compose 在本地系统上部署。

[使用 Docker Compose 启动 Elastic Stack](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-stack-docker.html#get-started-docker-tls)

![](Images/Day80_Monitoring1.png)

你可以在这里找到我使用的原始文件和演练：[deviantony/docker-elk](https://github.com/deviantony/docker-elk)

现在我们可以运行 `docker-compose up -d`，第一次运行时需要拉取镜像。

![](Images/Day80_Monitoring2.png)

如果你使用这个仓库或我使用的仓库，你的密码要么是 "changeme"，要么在我的仓库中是 "90DaysOfDevOps"。用户名是 "elastic"。

几分钟后，我们可以访问 http://localhost:5601/，这是我们的 Kibana 服务器 / Docker 容器。

![](Images/Day80_Monitoring3.png)

你的初始主页看起来会是这样的。

![](Images/Day80_Monitoring4.png)

在标题为 "Get started by adding integrations" 的部分下，有一个 "try sample data" 选项，点击它，我们可以添加下面显示的一个。

![](Images/Day80_Monitoring5.png)

我将选择 "Sample web logs"，但这实际上是为了了解可以将哪些数据集接入 ELK Stack。

当你选择了 "Add Data" 后，需要一段时间来填充一些数据，然后你会有 "View Data" 选项和一个下拉列表，显示查看该数据的可用方式。

![](Images/Day80_Monitoring6.png)

正如仪表板视图上所述：

**Sample Logs Data**

*This dashboard contains sample data for you to play with. You can view it, search it, and interact with the visualizations. For more information about Kibana, check our docs.*

![](Images/Day80_Monitoring7.png)

这是使用 Kibana 来可视化通过 Logstash 添加到 Elasticsearch 中的数据。这不是唯一的选择，但我个人想部署并看看这个。

我们将来会介绍 Grafana，你会看到两者之间有一些数据可视化的相似之处，你也已经见过 Prometheus 了。

我从中得到的关键收获是，Elastic Stack（或 ELK Stack）专注于日志，而 Prometheus 专注于指标。

我正在阅读 MetricFire 的这篇文章 [Prometheus vs. ELK](https://www.metricfire.com/blog/prometheus-vs-elk/)，以更好地了解两者的不同定位。

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

[第八十一天](day81.md)见。
