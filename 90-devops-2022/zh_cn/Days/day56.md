---
title: '#90DaysOfDevOps - 宏观图景：IaC - 第56天'
published: false
description: 90DaysOfDevOps - 宏观图景 IaC
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048709
---
## 宏观图景：IaC

人类会犯错！自动化才是王道！

你今天如何构建你的系统？

如果你今天失去一切，你的计划是什么？物理机、虚拟机、云虚拟机、云 PaaS 等等？

替换所有东西需要多长时间？

基础设施即代码提供了一种解决方案，能够完成此任务同时还能进行测试。我们不应将其与备份和恢复混淆，但就你的基础设施和环境、你的平台而言，我们应该能够启动它们，并将它们视为牲畜（cattle）而非宠物（pets）。

简而言之，就是我们可以使用代码来重建我们的整个环境。

如果我们还记得从一开始我们所说的，DevOps 总的来说是一种打破障碍的方式，以便将系统安全且快速地交付到生产环境。

基础设施即代码帮助我们交付系统，我们已经谈了很多流程和工具。IaC 为我们带来了更多需要熟悉的工具，以实现这一环节。

在本节中，我们将专注于基础设施即代码。你可能还会听到它被提到为 "Infrastructure from code" 或 "configuration as code"。我认为最知名的术语可能是基础设施即代码（Infrastructure as Code）。

### 宠物 vs 牲畜

如果我们回顾一下 DevOps 之前的时代，如果我们有构建新应用程序的需求，我们大部分情况下需要手动准备我们的服务器。

- 部署虚拟机 | 物理服务器并安装操作系统
- 配置网络
- 创建路由表
- 安装软件和更新
- 配置软件
- 安装数据库

这将是一个由系统管理员执行的手动过程。应用程序越大，需要的资源和服务器越多，启动这些系统所需的人工努力就越多。这将耗费大量的人力和时间，而且作为企业，你必须为构建此环境的资源付费。正如我在本节开头所说的："人类会犯错！自动化才是王道！"

从上述初始设置阶段开始，你还有这些服务器的维护工作。

- 更新版本
- 部署新版本
- 数据管理
- 应用程序恢复
- 添加、移除和扩展服务器
- 网络配置

再加上多个测试和开发环境的复杂性。

这就是基础设施即代码的用武之地。上述情况非常像我们会像照顾宠物一样照顾那些服务器，人们甚至给它们起了宠物的名字，或者至少给它们起了名字，因为它们会在那里待一段时间，它们希望能在"家庭"中待一段时间。

通过基础设施即代码，我们有能力自动化所有这些端到端的任务。基础设施即代码是一个概念，有一些工具可以执行这种基础设施的自动配置，此时如果服务器发生了不好的事情，你就扔掉它并启动一个新的。这个过程是自动化的，服务器完全按照代码中定义的那样。此时我们不关心它们叫什么，它们就在田野里服务于它们的用途，直到它们不再在田野里，我们有另一个来替换它，无论是由于故障还是因为我们更新了我们应用程序的一部分或全部。

这几乎可以在所有平台上使用：虚拟化、基于云的工作负载，以及云原生基础设施，如 Kubernetes 和容器。

### 基础设施配置

并非所有的 IaC 都涵盖以下所有内容，你会发现我们在本节中将要使用的工具实际上只涵盖以下前 2 个领域；Terraform 就是我们将要介绍的工具，它允许我们从无到有，在代码中定义我们的基础设施应该是什么样子，然后部署它。它还将使我们能够管理该基础设施，并最初部署一个应用程序，但到那个时候，它将失去对应用程序的跟踪，这就是下一节的内容，像 Ansible 这样的配置管理工具在这方面可能会更好地发挥作用。

不跳步的话，像 Chef、Puppet 和 Ansible 这样的工具最适合处理初始应用程序设置，然后管理这些应用程序及其配置。

软件的初始安装和配置

- 启动新服务器
- 网络配置
- 创建负载均衡器
- 基础设施级别的配置

### 已配置基础设施的配置

- 在服务器上安装应用程序
- 准备服务器以部署你的应用程序。

### 应用程序部署

- 部署和管理应用程序
- 维护阶段
- 软件更新
- 重新配置

### IaC 工具的区别

声明式 vs 过程式

过程式（Procedural）
- 逐步的指令
- 创建服务器 > 添加服务器 > 进行此更改

声明式（Declarative）
- 声明最终结果
- 2 台服务器

可变（Mutable / 宠物） vs 不可变（Immutable / 牲畜）

可变（Mutable）
- 更改而不是替换
- 通常是长寿命的

不可变（Immutable）
- 替换而不是更改
- 可能是短寿命的

这就是为什么我们有很多不同的基础设施即代码选项，因为没有一个工具可以统治一切。

我们将主要使用 Terraform 并进行实践，因为这是开始看到基础设施即代码在行动时的好处的最佳方式。实践也是掌握技能的最佳方式，因为你将要编写代码。

接下来，我们将在动手使用之前，开始了解 Terraform 101。

## 资源

我在下面列出了很多资源，我认为这个话题已经被涵盖了很多次，如果你有额外的资源，请务必提出 PR，我会很乐意审查并将它们添加到列表中。

- [What is Infrastructure as Code? Difference of Infrastructure as Code Tools](https://www.youtube.com/watch?v=POPP2WTJ8es)
- [Terraform Tutorial | Terraform Course Overview 2021](https://www.youtube.com/watch?v=m3cKkYXl-8o)
- [Terraform explained in 15 mins | Terraform Tutorial for Beginners](https://www.youtube.com/watch?v=l5k1ai_GBDE)
- [Terraform Course - From BEGINNER to PRO!](https://www.youtube.com/watch?v=7xngnjfIlK4&list=WL&index=141&t=16s)
- [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=V4waklkBC38&list=WL&index=55&t=111s)
- [Terraform Full Course for Beginners](https://www.youtube.com/watch?v=EJ3N-hhiWv0&list=WL&index=39&t=27s)
- [KodeKloud -  Terraform for DevOps Beginners + Labs: Complete Step by Step Guide!](https://www.youtube.com/watch?v=YcJ9IeukJL8&list=WL&index=16&t=11s)
- [Terraform Simple Projects](https://terraform.joshuajebaraj.com/)
- [Terraform Tutorial - The Best Project Ideas](https://www.youtube.com/watch?v=oA-pPa0vfks)
- [Awesome Terraform](https://github.com/shuaibiyy/awesome-terraform)

[第57天](day57.md)见
