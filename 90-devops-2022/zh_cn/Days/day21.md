---
title: '#90DaysOfDevOps - 全局视角：DevOps 与网络 - 第 21 天'
published: false
description: 90DaysOfDevOps - 全局视角：DevOps 与网络
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048761
---
## 全局视角：DevOps 与网络

欢迎来到第 21 天！接下来的 7 天里，我们将进入网络（Networking）主题。网络与 DevOps 是这一阶段的主线，但我们也需要学习一些网络基础知识。

归根结底，正如我们之前提到的，DevOps 关乎组织内部的文化和流程变革。我们讨论过的对象可以是虚拟机、容器、Kubernetes，但也可以是网络。如果我们要把这些 DevOps 原则应用到基础设施上，那么网络也必须包含在内。更准确地说，从 DevOps 的角度出发，你还需要了解网络，以及我们可用的不同拓扑、网络工具和技术栈。

我认为，我们应该像管理虚拟机一样，使用基础设施即代码（Infrastructure as Code）来配置网络设备，并把一切自动化。但要做到这一点，我们必须先很好地理解自己正在自动化的内容。

### 什么是 NetDevOps | 网络 DevOps？

你也可能听到 Network DevOps 或 NetDevOps 这两个术语。也许你本身就是一名网络工程师，对基础设施中的网络组件有很好的理解，熟悉 DHCP、DNS、NAT 等网络相关要素。你也会对硬件网络或软件定义网络方案、交换机、路由器等有较深的认识。

但如果你不是网络工程师，那么我们可能需要先在这些领域建立一些基础知识，这样才能理解 Network DevOps 的最终目标。

就这些术语而言，我们可以把 NetDevOps 或 Network DevOps 理解为：将 DevOps 的原则和实践应用到网络中，把版本控制和自动化工具用于网络的创建、测试、监控和部署。

如果我们把 Network DevOps 理解为必须依赖自动化，那么我们前面提到的 DevOps 打破团队之间孤岛的问题就更明显了。如果网络团队不转向类似的模型和流程，那么它们就可能成为瓶颈，甚至拖垮整体效率。

围绕资源开通、配置、测试、版本控制和部署来使用自动化原则，是一个很好的起点。总体来说，自动化会提升部署速度、增强网络基础设施的稳定性，并带来持续改进；一旦流程经过验证，它还可以在多个环境中共享。例如，一个在某个环境中经过充分测试的网络策略，可以很快被应用到另一个地点，因为它是代码化的，而不是像过去那样手工编写的流程。
关于这种思路，一个很好的观点和概述可以在这里找到：[Network DevOps](https://www.thousandeyes.com/learning/techtorials/network-devops)

## 网络基础

先暂时放下 DevOps 的部分，我们现在要非常简要地看看一些网络基础知识。

### 网络设备

**主机（Host）** 是任何能够发送或接收流量的设备。

![](Images/Day21_Networking1.png)

**IP 地址** 是每台主机的身份标识。

![](Images/Day21_Networking2.png)

**网络（Network）** 是在主机之间传输流量的媒介。如果没有网络，数据就只能靠大量人工搬运！

它也可以理解为一个需要类似连接能力的主机逻辑组。

![](Images/Day21_Networking3.png)

**交换机（Switch）** 负责促进网络***内部***的通信。交换机会在主机之间转发数据包，并将数据包直接发送给目标主机。

- 网络：一组需要类似连接能力的主机。
- 同一网络中的主机共享相同的 IP 地址空间。

![](Images/Day21_Networking4.png)

**路由器（Router）** 负责促进不同网络之间的通信。如果说交换机负责网络内部的通信，那么路由器则让我们能够把这些网络连接起来，或者至少在允许的情况下让它们彼此可达。

路由器可以提供流量控制点（安全、过滤、重定向）。现在越来越多的交换机也开始提供其中一些功能。

路由器会学习自己连接到了哪些网络，这些信息称为路由（routes）。路由表就是路由器所知道的所有网络。

路由器在所连接的网络中会有一个 IP 地址。这个 IP 也会成为每台主机离开本地网络的出口，也就是网关（gateway）。

路由器也会形成我前面提到的网络层级结构。

![](Images/Day21_Networking5.png)

## 交换机 vs 路由器

**路由（Routing）** 是在不同网络之间传输数据的过程。
    
- 路由器是其主要用途为路由的设备。

**交换（Switching）** 是在网络内部传输数据的过程。

- 交换机是其主要用途为交换的设备。

这只是对这些设备的基础概览，因为我们知道还有很多不同类型的网络设备，例如：

- 无线接入点（Access Points）
- 防火墙（Firewalls）
- 负载均衡器（Load Balancers）
- 三层交换机（Layer 3 Switches）
- IDS / IPS
- 代理（Proxies）
- 虚拟交换机（Virtual Switches）
- 虚拟路由器（Virtual Routers）

虽然这些设备中有很多都会执行路由和/或交换功能。

在接下来的几天里，我们会对这个列表中的内容有更多了解。

- OSI 模型
- 网络协议
- DNS（域名系统）
- NAT
- DHCP
- 子网

## 资源

[Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)

我们在 [第 22 天](day22.md) 再见
