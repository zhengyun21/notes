---
title: '#90DaysOfDevOps - 选择你的 Kubernetes 平台 - 第50天'
published: false
description: 90DaysOfDevOps - 选择你的 Kubernetes 平台
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049046
---
## 选择你的 Kubernetes 平台

我想用这节课来分解一些平台，或者也许用发行版（distribution）这个词更合适。在 Kubernetes 世界中，有一件事一直是个挑战，那就是消除复杂性。

Kubernetes the hard way 展示了如何从零开始构建一个功能完整的 Kubernetes 集群，这显然是一个极端的例子，但越来越多至少是我交谈过的人都希望消除这种复杂性，运行一个托管的 Kubernetes 集群。那里的问题是，它的成本更高，但如果你使用托管服务，你真的需要了解底层节点架构以及从控制平面节点的角度来看发生了什么吗？通常你无法访问这些。

然后我们还有本地开发发行版，使我们能够使用自己的系统并运行 Kubernetes 的本地版本，这样开发人员就可以拥有完整的工作环境来在他们预期的平台中运行他们的应用程序。

所有这些概念的一般基础是，它们都是 Kubernetes 的一种变体，这意味着我们应该能够自由地迁移和移动我们的工作负载，以满足我们的需求。

我们的很多选择还将取决于已经进行了哪些投资。我提到了开发人员体验，但那些在我们的笔记本电脑上运行的本地 Kubernetes 环境对于在不花钱的情况下掌握这项技术非常有用。

### 裸机集群（Bare-Metal Clusters）

对许多人来说，一个选择是将 Linux 操作系统直接安装到多台物理服务器上以创建我们的集群，也可以是 Windows，但我没有听说过太多关于 Windows、容器和 Kubernetes 的采用率。显然，如果你是一家企业，并且你已经做出了购买物理服务器的资本支出（CAPEX）决策，那么这可能是你在构建 Kubernetes 集群时会选择的方式，这里的管理和维护意味着你将不得不从头开始自己构建和管理一切。

### 虚拟化（Virtualisation）

无论是测试和学习环境还是企业级的 Kubernetes 集群，虚拟化都是一个很好的选择，通常能够启动虚拟机作为你的节点，然后将它们集群在一起。你拥有虚拟化的底层架构、效率和速度，以及利用现有支出的优势。例如，VMware 提供了多种不同形式的虚拟机和 Kubernetes 解决方案。

我的第一个 Kubernetes 集群是基于虚拟化构建的，使用 Microsoft Hyper-V 在一台旧服务器上运行几个虚拟机作为我的节点。

### 本地桌面选项

在桌面或笔记本电脑上运行本地 Kubernetes 集群时，有很多选择。如前所述，这让开发人员能够了解他们的应用程序会是什么样子，而无需拥有多个昂贵或复杂的集群。就个人而言，这是我使用过很多的一个选项，特别是我一直在使用 minikube。它有一些很棒的功能和插件，可以改变你启动和运行某些东西的方式。

### Kubernetes 托管服务

我提到了虚拟化，这可以通过本地管理程序实现，但我们从前面的章节知道，我们也可以利用公共云中的虚拟机作为我们的节点。我在这里所说的 Kubernetes 托管服务是指我们从大型超大规模云厂商以及 MSP（托管服务提供商）那里看到的，它们将管理和控制层从最终用户那里移除了，这可能是将控制平面从最终用户那里移除，这就是 Amazon EKS、Microsoft AKS 和 Google Kubernetes Engine（GKE）所做的事情。

### 令人眼花缭乱的选择

我的意思是，选择固然很好，但有一个点事情会变得令人不知所措，而这实际上并不是对上述每个类别中所有选项的深入研究。除此之外，我们还有 OpenShift，它来自 Red Hat，这个选项实际上可以在上述所有选项中运行，在所有主要的云提供商中，而且今天可能为管理员提供了最好的整体可用性，无论集群部署在哪里。

那么从学习的角度来看，你应该从哪里开始呢？正如我所说，我从虚拟化路线开始，但那是因为我可以使用一台物理服务器来用于此目的，我很感激，事实上从那以后我不再有这个选项了。

我现在的实际建议是首先使用 Minikube 或 Kind（Kubernetes in Docker），但 Minikube 为我们提供了一些额外的好处，几乎将复杂性抽象掉了，因为我们可以直接使用插件，非常快速地构建东西，完成后可以销毁它，我们可以运行多个集群，几乎可以在任何地方运行，跨平台且与硬件无关。

我在 Kubernetes 学习方面经历了一段旅程，所以我会把平台选择和细节留在这里，列出我为了解 Kubernetes 平台及其可以运行的地方而尝试过的选项。我可能会对下面的博客文章再进行一次回顾，更新它们，并将它们更多地融入这里，而不是作为博客文章的链接。

- [Kubernetes playground – How to choose your platform](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-1)
- [Kubernetes playground – Setting up your cluster](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-2)
- [Getting started with Amazon Elastic Kubernetes Service (Amazon EKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-amazon-elastic-kubernetes-service-amazon-eks)
- [Getting started with Microsoft Azure Kubernetes Service (AKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-azure-kubernetes-service-aks)
- [Getting Started with Microsoft AKS – Azure PowerShell Edition](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-aks-azure-powershell-edition)
- [Getting started with Google Kubernetes Service (GKE)](https://vzilla.co.uk/vzilla-blog/getting-started-with-google-kubernetes-service-gke)
- [Kubernetes, How to – AWS Bottlerocket + Amazon EKS](https://vzilla.co.uk/vzilla-blog/kubernetes-how-to-aws-bottlerocket-amazon-eks)
- [Getting started with CIVO Cloud](https://vzilla.co.uk/vzilla-blog/getting-started-with-civo-cloud)
- [Minikube - Kubernetes Demo Environment For Everyone](https://vzilla.co.uk/vzilla-blog/project_pace-kasten-k10-demo-environment-for-everyone)

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

[第51天](day51.md)见。
