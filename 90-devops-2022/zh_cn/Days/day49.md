---
title: '#90DaysOfDevOps - 全景图：Kubernetes - 第49天'
published: false
description: 90DaysOfDevOps - 全景图 Kubernetes
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049049
---
## 全景图：Kubernetes

在上一节中，我们介绍了容器。容器在扩展和编排方面存在不足。我们最多能做的就是使用 docker-compose 来一起启动多个容器。当谈到 Kubernetes（一个容器编排器）时，它使我们能够根据应用程序和服务的负载以自动化方式或基于负载进行扩缩容。

作为一个平台，Kubernetes 提供了根据你的要求和期望状态来编排容器的能力。我们将在本节中介绍 Kubernetes，因为它正迅速成为下一代基础设施。我还建议，从 DevOps 的角度来看，Kubernetes 只是你需要基本了解的一个平台，你还需要了解裸机、虚拟化，以及很可能基于云的服务。Kubernetes 只是运行我们应用程序的另一个选项。

### 什么是容器编排？

我提到了 Kubernetes，也提到了容器编排。Kubernetes 是技术，而容器编排是技术背后的概念或流程。Kubernetes 不是唯一的容器编排平台，我们还有 Docker Swarm、HashiCorp Nomad 等。但 Kubernetes 正在变得越来越强大，所以我想介绍 Kubernetes，但也要说明它不是唯一的选择。

### 什么是 Kubernetes？

如果你是 Kubernetes 的新手，你应该阅读的第一件事就是官方文档。我在一年多前真正深入钻研 Kubernetes 时的经验是，这将是一个陡峭的学习曲线。来自虚拟化和存储背景的我，觉得这看起来多么令人生畏。

但实际上，社区、免费学习资源和文档实际上非常棒。[Kubernetes.io](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

*Kubernetes 是一个可移植、可扩展的开源平台，用于管理容器化的工作负载和服务，它促进了声明式配置和自动化。它拥有一个庞大且快速增长的生态系统。Kubernetes 的服务、支持和工具广泛可用。*

从上面的引用中需要注意的重要事项：Kubernetes 是开源的，有着悠久的历史，可以追溯到 Google，Google 将该项目捐赠给了 Cloud Native Computing Foundation（CNCF），现在它由开源社区以及大型企业供应商共同推动，使 Kubernetes 成为今天的样子。

我在上面提到容器很棒，在上一节中我们谈到了容器和容器镜像如何改变和加速了云原生系统的采用。但仅靠容器并不能为你提供应用程序所需的生产就绪体验。Kubernetes 为我们提供了以下功能：

- **服务发现和负载均衡** Kubernetes 可以使用 DNS 名称或它们自己的 IP 地址来暴露容器。如果到容器的流量很高，Kubernetes 能够进行负载均衡并分配网络流量，从而使部署保持稳定。

- **存储编排** Kubernetes 允许你自动挂载你选择的存储系统，例如本地存储、公共云提供商等。

- **自动部署和回滚** 你可以使用 Kubernetes 描述已部署容器的期望状态，它可以以受控的速率将实际状态更改为期望状态。例如，你可以自动化 Kubernetes 为你的部署创建新容器、删除现有容器并将其所有资源采纳到新容器。

- **自动装箱** 你为 Kubernetes 提供一个可以使用的节点集群来运行容器化任务。你告诉 Kubernetes 每个容器需要多少 CPU 和内存（RAM）。Kubernetes 可以将容器放到你的节点上，以最佳利用你的资源。

- **自我修复** Kubernetes 会重新启动失败的容器、替换容器、终止不响应用户定义健康检查的容器，并且在它们准备好提供服务之前不会将它们通告给客户端。

- **密钥和配置管理** Kubernetes 允许你存储和管理敏感信息，例如密码、OAuth 令牌和 SSH 密钥。你可以在不重建容器镜像且不暴露密钥的情况下部署和更新密钥和应用程序配置。

Kubernetes 为你提供了一个框架来弹性地运行分布式系统。

容器编排管理容器的部署、放置和生命周期。

它还有许多其他职责：

- **集群管理** 将主机联合成一个目标。

- **调度管理** 通过调度器将容器分发到各个节点。

- **服务发现** 知道容器的位置并将客户端请求分发到它们。

- **副本管理** 确保为请求的工作负载提供正确数量的节点和容器。

- **健康管理** 检测并替换不健康的容器和节点。

### Kubernetes 主要组件

Kubernetes 是一个容器编排器，用于配置、管理和扩展应用程序。你可以使用它来管理集群中节点上容器化应用程序的生命周期，节点是虚拟机（VM）或物理机等工作机器的集合。

你的应用程序可能需要许多其他资源才能运行，例如卷、网络和密钥，这些资源可以帮助你连接数据库、与受防火墙保护的后端通信以及保护密钥。使用 Kubernetes，你可以将这些资源添加到你的应用程序中。应用程序所需的基础设施资源以声明式方式进行管理。

Kubernetes 的关键范式是其声明式模型。你提供你想要的状态，Kubernetes 就会实现它。如果你需要五个实例，你不会自己启动五个单独的实例。相反，你告诉 Kubernetes 你需要五个实例，Kubernetes 会自动调和状态。如果你的某个实例出现问题并失败了，Kubernetes 仍然知道你想要的​​状态，并会在可用节点上创建实例。

### 节点（Node）

**控制平面（Control Plane）**

每个 Kubernetes 集群都需要一个控制平面节点，控制平面的组件做出关于集群的全局决策（例如调度），以及检测和响应集群事件。

![](Images/Day49_Kubernetes1.png)

**工作节点（Worker Node）**

运行 Kubernetes 工作负载的工作机器。它可以是物理机（裸机）或虚拟机（VM）。每个节点可以托管一个或多个 Pod。Kubernetes 节点由控制平面管理。

![](Images/Day49_Kubernetes2.png)

还有其他节点类型，但我不会在这里介绍它们。

**kubelet**

在集群中的每个节点上运行的代理。它确保容器在 Pod 中运行。

kubelet 接收通过各种机制提供的一组 PodSpec，并确保这些 PodSpec 中描述的容器正在运行且健康。kubelet 不管理不是由 Kubernetes 创建的容器。

![](Images/Day49_Kubernetes3.png)

**kube-proxy**

kube-proxy 是在集群中的每个节点上运行的网络代理，实现了 Kubernetes 服务概念的一部分。

kube-proxy 维护节点上的网络规则。这些网络规则允许从集群内或集群外的网络会话与 Pod 进行网络通信。

如果存在并且可用，kube-proxy 使用操作系统数据包过滤层。否则，kube-proxy 会自己转发流量。

![](Images/Day49_Kubernetes4.png)

**容器运行时（Container runtime）**

容器运行时是负责运行容器的软件。

Kubernetes 支持多种容器运行时：Docker、containerd、CRI-O 以及任何实现 Kubernetes CRI（Container Runtime Interface，容器运行时接口）的实现。

![](Images/Day49_Kubernetes5.png)

### 集群（Cluster）

集群是一组节点，其中节点可以是物理机或虚拟机。每个节点都将具有容器运行时（Docker），并且还将运行一个 kubelet 服务，这是一个接收来自主控制器（稍后会详细介绍）命令的代理，以及一个代理（Proxy），用于将来自另一个组件（服务，我们稍后会看到）的连接代理到 Pod。

在我们的控制平面上，可以实现高可用性，与 worker 节点相比，它将包含一些独特的角色，其中最重要的是 kube API server，任何获取信息或向 Kubernetes 集群推送信息的通信都将在这里进行。

**Kube API-Server**

Kubernetes API server 验证并配置 api 对象的数据，包括 Pod、服务、replicationcontrollers 等。API Server 为 REST 操作提供服务，并提供集群共享状态的前端，所有其他组件都通过它与集群交互。

**Scheduler**

Kubernetes 调度器是一个控制平面进程，负责将 Pod 分配给节点。调度器根据约束和可用资源确定调度队列中每个 Pod 的有效节点位置。然后调度器对每个有效节点进行排名，并将 Pod 绑定到合适的节点。

**Controller Manager**

Kubernetes 控制器管理器是一个守护进程，它嵌入了与 Kubernetes 一起交付的核心控制循环。在机器人技术和自动化应用中，控制循环是一个永不终止的循环，用于调节系统的状态。在 Kubernetes 中，控制器是一个控制循环，它通过 apiserver 监视集群的共享状态，并做出更改，试图将当前状态移向期望状态。

**etcd**

一致且高可用的键值存储，用作 Kubernetes 所有集群数据的备份存储。

![](Images/Day49_Kubernetes6.png)

**kubectl**

为了从 CLI 角度管理这一切，我们有 kubectl，kubectl 与 API server 交互。

Kubernetes 命令行工具 kubectl 允许你对 Kubernetes 集群运行命令。你可以使用 kubectl 来部署应用程序、检查和管理集群资源以及查看日志。

![](Images/Day49_Kubernetes7.png)

### Pod

Pod 是构成逻辑应用程序的一组容器。例如，如果你有一个运行 NodeJS 容器和 MySQL 容器的 Web 应用程序，那么这两个容器将位于同一个 Pod 中。Pod 还可以共享公共数据卷，并且它们还共享相同的网络命名空间。请记住，Pod 是短暂的（ephemeral），它们可能被主控制器启动和关闭。Kubernetes 使用一种简单而有效的方式通过标签（Label）的概念（名称 — 值）来识别 Pod。

- Pod 处理容器的卷、密钥和配置。

- Pod 是短暂的。当它们死亡时会被自动重启。

- 当应用被 ReplicaSet 水平扩展时，Pod 会被复制。每个 Pod 将运行相同的容器代码。

- Pod 存在于 Worker 节点上。

![](Images/Day49_Kubernetes8.png)

### 部署（Deployments）

- 你可以决定直接运行 Pod，但当它们死亡时，它们就真的死了。

- Deployment 将使你的 Pod 持续运行。

- Deployment 允许你在不停机的情况下更新正在运行的应用程序。

- Deployment 还指定了在 Pod 死亡时重启它们的策略。

![](Images/Day49_Kubernetes9.png)

### ReplicaSet

- Deployment 还可以创建 ReplicaSet。

- ReplicaSet 确保你的应用程序具有所需数量的 Pod。

- ReplicaSet 将根据 Deployment 创建和扩展 Pod。

- Deployment、ReplicaSet、Pod 并非互斥，而是可以协同工作。


### StatefulSet

- 你的应用程序是否需要保存有关其状态的信息？

- 数据库需要状态。

- StatefulSet 的 Pod 不是可互换的。

- 每个 Pod 都有一个唯一的、持久的标识符，控制器在任何重新调度时都会维护该标识符。

- 每个 Pod 都有一个唯一的、持久的标识符，控制器在任何重新调度时都会维护该标识符。

![](Images/Day49_Kubernetes10.png)

### DaemonSet

- DaemonSet 用于持续运行的进程。

- 它们在每个节点上运行一个 Pod。

- 添加到集群的每个新节点都会启动一个 Pod。

- 适用于后台任务，例如监控和日志收集。

- 每个 Pod 都有一个唯一的、持久的标识符，控制器在任何重新调度时都会维护该标识符。

![](Images/Day49_Kubernetes11.png)

### 服务（Services）

- 访问 Pod 的单一端点。

- 一种将流量路由到集群并最终路由到一组 Pod 的统一方式。

- 通过使用 Service，Pod 可以被启动和关闭而不会影响任何东西。

这只是对 Kubernetes 基本构建块的快速概述和笔记，我们可以利用这些知识并在存储和 Ingress 等其他领域进行补充，以增强我们的应用程序，但我们还有很多关于 Kubernetes 集群运行位置的选择。下一节将重点介绍这些关于在哪里可以运行 Kubernetes 集群的选项，同时还会探索一些关于存储的具体内容。

![](Images/Day49_Kubernetes12.png)

### 我们将在 Kubernetes 系列中涵盖的内容

- Kubernetes 架构
- Kubectl 命令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes 服务
- Helm 包管理器
- 持久化存储
- 有状态应用

## 资源

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

[第50天](day50.md)见。
