---
title: '#90DaysOfDevOps - Microsoft Azure 计算模型 - 第31天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 计算模型
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049040
---
## Microsoft Azure 计算模型

继昨天介绍 Microsoft Azure 安全模型的基础知识之后，今天我们将研究 Azure 中可用的各种计算服务。

### 服务可用性选项

鉴于我在数据管理领域的角色，这一部分与我的心很贴近。与本地部署一样，确保服务的可用性至关重要。

- 高可用性（High Availability，在一个区域内提供保护）
- 灾难恢复（Disaster Recovery，在区域之间提供保护）
- 备份（Backup，从某个时间点恢复）

Microsoft 在地缘政治边界内部署了多个区域。

Azure 中有两个关于服务可用性的概念：可用性集和可用性区域。

可用性集（Availability Sets）- 在数据中心内提供弹性

可用性区域（Availability Zones）- 在一个区域内的数据中心之间提供弹性。

### 虚拟机（Virtual Machines, VM）

这很可能是任何进入公有云的人的起点。

- 提供各种系列和尺寸的 VM，具有不同的功能（有时会让人不知所措）[Azure 虚拟机尺寸](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes)
- VM 有许多不同的选项和侧重点，从高性能、低延迟到高内存选项 VM。
- 我们还有一种可突发 VM 类型，可以在 B 系列下找到。这对于大多数时间 CPU 需求较低但可能每月需要一次性能峰值的工作负载非常有用。
- 虚拟机放置在虚拟网络上，该虚拟网络可以提供与任何网络的连接。
- 支持 Windows 和 Linux 客户机操作系统。
- 对于特定的 Linux 发行版，还有 Azure 调优内核。[Azure 调优内核](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/endorsed-distros#azure-tuned-kernels)

### 模板（Templating）

我之前提到过，Microsoft Azure 背后或底层的一切都是 JSON。

我们有几个不同的管理门户和控制台可以用来创建资源，首选途径是通过 JSON 模板。

幂等部署，以增量或完整模式进行 - 即可重复的期望状态。

有大量模板可以导出已部署资源的定义。我喜欢将这个模板功能类比为 AWS CloudFormation，或者对于多云选项来说类比为 Terraform。我们将在基础设施即代码（Infrastructure as Code）部分更多地介绍 Terraform。

### 扩展（Scaling）

自动扩展是公有云的一大特性，能够关闭你不使用的资源或在需要时启动它们。

在 Azure 中，对于 IaaS，我们有称为虚拟机规模集（Virtual Machine Scale Sets, VMSS）的功能。这使得能够根据计划表和指标从黄金标准镜像自动创建和扩展。

这对于更新 Windows 非常理想，以便你可以更新镜像并以最小的影响推出它们。

其他服务（例如 Azure App Services）具有内置的自动扩展功能。

### 容器（Containers）

我们尚未涉及容器作为用例，以及它们在我们的 DevOps 学习旅程中需要和需求的内容，但我们需要提到 Azure 有一些特定的容器-focused 服务值得一提。

Azure Kubernetes Service（AKS）- 提供托管的 Kubernetes 解决方案，无需担心控制平面或底层集群管理的维护。稍后也会更多地介绍 Kubernetes。

Azure Container Instances - 容器即服务，按秒计费。运行镜像并将其与虚拟网络集成，无需容器编排。

Service Fabric - 具有许多功能，但包括容器实例的编排。

Azure 还有 Container Registry（容器注册表），它为 Docker 镜像、Helm 图表、OCI 构件和镜像提供私有注册表。当我们到达容器部分时，还会再次介绍这个。

我们还应该提到，许多容器服务实际上也可能在底层利用容器，但这从你需要管理的要求中抽象出来了。

这些提到的容器-focused 服务，我们在所有其他公有云中也可以找到类似的服务。

### 应用程序服务（Application Services）

- Azure Application Services 提供了一种应用程序托管解决方案，提供了建立服务的简便方法。
- 自动部署和扩展。
- 支持基于 Windows 和 Linux 的解决方案。
- 服务在应用服务计划（App Service Plan）中运行，该计划具有类型和大小。
- 多种不同的服务，包括 Web 应用、API 应用和移动应用。
- 支持部署槽（Deployment slots）用于可靠的测试和提升。

### 无服务器计算（Serverless Computing）

对我来说，无服务器是我非常感兴趣并希望更多了解的令人兴奋的下一步。

无服务器的目标是我们只需为函数的运行时间付费，而不必一直运行虚拟机或 PaaS 应用程序。我们只需在需要时运行函数，然后它就消失了。

Azure Functions - 提供无服务器代码。如果我们回想一下第一次查看公有云时的管理层抽象，使用无服务器函数时，你只需管理代码。

事件驱动（Event-Driven）的大规模扩展，我有一个计划，当我在这里进行一些动手实践时，希望稍后能构建一些东西。

提供输入和输出绑定到许多 Azure 和第三方服务。

支持许多不同的编程语言。（C#、NodeJS、Python、PHP、batch、bash、Golang 和 Rust。或任何可执行文件）

Azure Event Grid 使逻辑能够从服务和事件触发。

Azure Logic App 提供基于图形的工作流和集成。

我们还可以查看 Azure Batch，它可以在 Windows 和 Linux 节点上运行大规模作业，具有一致的管理和调度。

## 资源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

[第32天](day32.md) 见