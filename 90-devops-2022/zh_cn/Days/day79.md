---
title: '#90DaysOfDevOps - 全局视角：日志管理 - 第79天'
published: false
description: 90DaysOfDevOps - 全局视角：日志管理
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049057
---
## 全局视角：日志管理

作为基础设施监控挑战与解决方案的延续，日志管理是整体可观测性拼图中的另一块关键拼板。

### 日志管理与聚合

让我们谈谈两个核心概念，首先是日志聚合，它是一种从许多不同服务收集和标记应用日志并将其汇聚到单个可轻松搜索的仪表板的方法。

在应用性能管理系统中，首先要构建的系统之一就是日志聚合。应用性能管理（Application Performance Management，APM）是 DevOps 生命周期的一部分，此时应用已经构建并部署完成，你需要确保它们持续正常运行，分配足够的资源，并且不会向用户展示错误。在大多数生产部署中，有许多相关事件会在各个服务中产生日志。在 Google，一次搜索可能会经过十个不同的服务才会返回给用户。如果你得到了意外的搜索结果，那可能意味着这十个服务中的任何一个存在逻辑问题。日志聚合帮助像 Google 这样的公司诊断生产中的问题，他们构建了一个单一的仪表板，可以将每个请求映射到唯一的 ID，所以当你搜索某些内容时，你的搜索会获得一个唯一 ID，然后每次搜索通过不同服务时，该服务都会将这个 ID 与其正在执行的操作关联起来。

这就是优秀的日志聚合平台的本质：高效地从所有产生日志的地方收集日志，并在发生故障时使其易于搜索。

### 示例应用

我们的示例应用是一个 Web 应用，具有典型的前端和后端，将关键数据存储到 MongoDB 数据库中。

如果有用户告诉我们页面全白了并显示了一条错误信息，我们很难用当前的栈来诊断问题。用户需要手动将错误发送给我们，我们需要将其与其他三个服务中的相关日志进行匹配。

### ELK

让我们来看看 ELK，这是一个流行的开源日志聚合栈，以其三个组件命名：Elasticsearch、Logstash 和 Kibana。如果我们在示例应用相同的环境中安装它。

Web 应用会连接到前端，前端再连接到后端，后端会将日志发送到 Logstash，这三个组件的工作方式如下。

### ELK 的组件

Elasticsearch、Logstash 和 Kibana 的工作方式是：所有服务都将日志发送到 Logstash，Logstash 接收这些日志（即应用发出的文本）。例如，当你访问一个 Web 页面时，Web 应用可能会记录 "访客于此时访问了此页面"，这就是一个日志消息的示例，这些日志会被发送到 Logstash。

然后 Logstash 会从日志中提取信息。对于那条 "用户在**某个时间**做了**某件事**" 的日志消息，它会提取时间、提取消息内容、提取用户，并将这些都作为标签，这样这条消息就变成了一个由标签和消息组成的对象，便于搜索。你可以找到某个特定用户的所有请求。但 Logstash 本身不存储数据，它将数据存储在 Elasticsearch 中，Elasticsearch 是一个高效的文本查询数据库，并将结果以 Kibana 的形式展示出来。Kibana 是一个连接到 Elasticsearch 的 Web 服务器，允许管理员、DevOps 人员或团队中的其他人、值班工程师在生产环境发生严重故障时查看日志。作为管理员，你会连接到 Kibana，Kibana 会向 Elasticsearch 查询符合你需求的日志。

你可以在搜索栏中对 Kibana 说：我想查找错误，然后 Kibana 会让 Elasticsearch 查找包含 "error" 字符串的消息，Elasticsearch 会返回由 Logstash 填充的结果，而 Logstash 会从所有其他服务接收这些结果。

### 我们如何使用 ELK 诊断生产问题

用户说：当我尝试做某事时看到了错误码 1234567。如果设置了 ELK，我们只需进入 Kibana，在搜索栏中输入 1234567，按回车，然后就会显示与之对应的日志。其中某条日志可能会说 "内部服务器错误，返回 1234567"，我们会看到发出该日志的服务是后端，并且能看到该日志发出的时间，因此我们可以跳到该日志的时间点，查看后端中该时间点前后的消息，从而更清晰地了解用户请求发生了什么。我们可以重复这个过程，前往其他服务，直到找到真正导致用户问题的原因。

### 日志的安全与访问

一个重要的环节是确保日志仅对管理员可见（或绝对需要访问权限的用户和组）。日志可能包含敏感信息，如令牌，因此只有经过身份验证的用户才能访问它们。你不希望在没有某种身份验证方式的情况下将 Kibana 暴露到互联网上。

### 日志管理工具示例

日志管理平台示例包括：

- Elasticsearch
- Logstash
- Kibana
- Fluentd - 流行的开源选择
- Datadog - 托管服务，大型企业常用
- LogDNA - 托管服务
- Splunk

云提供商也提供日志服务，如 AWS CloudWatch Logs、Microsoft Azure Monitor 和 Google Cloud Logging。


日志管理是应用和基础设施环境整体可观测性的关键方面，对于诊断生产中的问题至关重要。安装像 ELK 或 CloudWatch 这样的现成解决方案相对简单，它能显著简化和加速生产中问题的诊断与分类。

## 相关资料

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

[第八十天](day80.md)见。
