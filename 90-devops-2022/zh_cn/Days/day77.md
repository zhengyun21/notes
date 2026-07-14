---
title: '#90DaysOfDevOps - 全景图：监控 - 第77天'
published: false
description: 90DaysOfDevOps - 全景图监控
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048715
---
## 全景图：监控

在本节中，我们将讨论监控，什么是监控，为什么我们需要它？

### 什么是监控？

监控是对整个基础设施保持密切关注的过程。

### 为什么我们需要它？

假设我们正在管理一千台服务器，这些服务器包括各种专用服务器，如应用服务器、数据库服务器和 web 服务器。我们还可以用额外的服务和不同的平台（包括公有云产品和 Kubernetes）来进一步使情况复杂化。

![](Images/Day77_Monitoring1.png)

我们负责确保服务器上的所有服务、应用和资源都按预期运行。

![](Images/Day77_Monitoring2.png)

我们该怎么做？有三种方式：

- 手动登录到我们所有的服务器，检查与服务、进程和资源相关的所有数据。
- 编写一个脚本，帮我们登录到服务器并检查数据。

这两个选项都需要我们付出相当大的工作量。

第三个选项更简单，我们可以使用市场上可用的监控解决方案。

Nagios 和 Zabbix 是现成的可用解决方案，它们允许我们扩展监控基础设施，以包含任意数量的服务器。

### Nagios

Nagios 是一个基础设施监控工具，由一家同名公司制造。这个工具的开源版本称为 Nagios Core，商业版本称为 Nagios XI。[Nagios 官网](https://www.nagios.org/)

该工具允许我们监控服务器，查看它们是否被充分利用，或者是否有任何需要处理的故障任务。

![](Images/Day77_Monitoring3.png)

本质上，监控使我们能够实现这两个目标：检查服务器和服务的状态，并确定基础设施的健康状况。它还为我们提供了完整基础设施的 40,000 英尺视图，以查看我们的服务器是否正在运行，应用是否正常工作，web 服务器是否可达。

它会告诉我们某台特定服务器的磁盘在过去 10 周内一直以 10% 的速度增长，它将在未来四五天内完全耗尽，并且很快就会无响应。当你的磁盘或服务器处于临界状态时，它会向我们发出警报，以便我们可以采取适当的措施来避免可能的中断。

在这种情况下，我们可以释放一些磁盘空间，确保服务器不会故障，我们的用户也不会受到影响。

对大多数监控工程师来说，困难的问题是：我们监控什么？反过来，我们不监控什么？

每个系统都有许多资源，我们应该密切关注哪些资源，又可以对哪些视而不见？例如，是否有必要监控 CPU 使用率，答案显然是肯定的。然而，这仍然是一个必须做出的决定。是否有必要监控系统中开放端口的数量？我们可能必须监控，也可能不必，这取决于具体情况。如果是一台通用服务器，我们可能不必监控，但如果是 web 服务器，我们可能就需要监控。

### 持续监控

监控并不是什么新鲜事，甚至持续监控也已经是许多企业多年来采用的理想做法。

在监控方面，有三个关键的关注领域。

- 基础设施监控（Infrastructure Monitoring）
- 应用监控（Application Monitoring）
- 网络监控（Network Monitoring）

需要注意的重要一点是，有许多工具可用，我们在本节中提到了两个通用的系统和工具，但还有很多。监控解决方案的真正好处在于，当你真正花时间确保你回答了那个问题：我们应该监控什么，不应该监控什么？

我们可以在任何平台中开启监控解决方案，它会开始抓取信息，但如果信息量过大，你将很难从中受益，你必须花时间进行配置。

在下一节 session 中，我们将动手实践一个监控工具，看看我们可以开始监控什么。

## 资源

- [The Importance of Monitoring in DevOps](https://www.devopsonline.co.uk/the-importance-of-monitoring-in-devops/)
- [Understanding Continuous Monitoring in DevOps?](https://medium.com/devopscurry/understanding-continuous-monitoring-in-devops-f6695b004e3b)
- [DevOps Monitoring Tools](https://www.youtube.com/watch?v=Zu53QQuYqJ0)
- [Top 5 - DevOps Monitoring Tools](https://www.youtube.com/watch?v=4t71iv_9t_4)
- [How Prometheus Monitoring works](https://www.youtube.com/watch?v=h4Sl21AKiDg)
- [Introduction to Prometheus monitoring](https://www.youtube.com/watch?v=5o37CGlNLr8)

[第78天](day78.md)见。
