---
title: '#90DaysOfDevOps - Terraform 简介 - 第57天'
published: false
description: 90DaysOfDevOps - Terraform 简介
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048710
---
## Terraform 简介

"Terraform 是一个用于安全高效地构建、更改和版本化基础设施的工具"

上面的引用来自 HashiCorp，HashiCorp 是 Terraform 背后的公司。

"Terraform 是一个开源的基础设施即代码软件工具，它提供了一致的 CLI 工作流来管理数百种云服务。Terraform 将云 API 编码为声明式配置文件。"

HashiCorp 有一个很棒的资源 [HashiCorp Learn](https://learn.hashicorp.com/terraform?utm_source=terraform_io&utm_content=terraform_io_hero)，涵盖了他们所有的产品，并提供了一些很棒的演示教程，当你尝试使用基础设施即代码实现某些功能时非常有用。

所有云提供商和本地平台通常都会为我们提供管理控制台，使我们能够通过 UI 创建资源。一般来说，这些平台还提供 CLI 或 API 访问来创建相同的资源，但通过 API，我们有能力快速配置。

基础设施即代码允许我们接入这些 API，以期望的状态部署我们的资源。

其他工具，以下并非排他性或详尽的列表。如果你有其他工具，请通过 PR 分享。

| 云特定                          | 云无关         | 
| ------------------------------- | -------------- |
| AWS CloudFormation              | Terraform      | 
| Azure Resource Manager          | Pulumi         | 
| Google Cloud Deployment Manager |                | 

这也是我们使用 Terraform 的另一个原因，我们希望对我们希望用于演示的基础设施和平台保持无关性，但一般来说也是如此。

## Terraform 概览

Terraform 是一个专注于配置的工具，Terraform 是一个 CLI，它提供了能够配置复杂基础设施环境的能力。使用 Terraform，我们可以定义存在于本地或远程（云端）的复杂基础设施需求。Terraform 不仅使我们能够最初构建事物，还能在其生命周期内维护和更新这些资源。

我们将在这里介绍高层次的内容，但更多详细信息和大量资源，你可以前往 [terraform.io](https://www.terraform.io/)

### 编写（Write）

Terraform 允许我们创建声明式配置文件来构建我们的环境。文件使用 HashiCorp Configuration Language (HCL) 编写，它允许使用块、参数和表达式来简洁地描述资源。我们当然会在部署虚拟机、容器和 Kubernetes 中详细研究这些。


### 计划（Plan）

能够检查上述配置文件是否将部署我们想要看到的内容，使用 Terraform CLI 的特定功能来能够在部署或更改任何内容之前测试该计划。请记住，Terraform 是你基础设施的持续工具，如果你想更改基础设施的某个方面，你应该通过 Terraform 来完成，以便所有这些都被捕获在代码中。

### 应用（Apply）

显然，一旦你对结果满意，你就可以继续将此配置应用到 Terraform 中可用的众多提供商。你可以 [在这里](https://registry.terraform.io/browse/providers) 看到大量的可用提供商。

另一件要提到的事情是，还有模块可用，这类似于容器镜像，因为这些模块已经被创建并在公共领域共享，所以你不必一遍又一遍地创建它，只需重复使用部署特定基础设施资源的最佳实践，使其在任何地方都以相同的方式进行。你可以在 [这里](https://registry.terraform.io/browse/modules) 找到可用的模块。


Terraform 工作流如下所示：（*摘自 Terraform 官网*）

![](Images/Day57_IAC3.png)

### Terraform vs Vagrant

在本次挑战中，我们使用了 Vagrant，它恰好是另一个 HashiCorp 开源工具，专注于开发环境。

- Vagrant 是一个专注于管理开发环境的工具

- Terraform 是一个构建基础设施的工具。

关于这两个工具的一个很好的比较可以在官方的 [HashiCorp 网站](https://www.vagrantup.com/intro/vs/terraform) 上找到。


## Terraform 安装

Terraform 的安装实际上没什么复杂的。

Terraform 是跨平台的，你可以在我的 Linux 机器上看到下面有几种下载和安装 CLI 的选项。

![](Images/Day57_IAC2.png)


使用 `arkade` 安装 Terraform，arkade 是一个方便的小工具，用于将所需的工具、应用程序和 CLI 安装到你的系统上。简单的 `arkade get terraform` 将允许在有可用更新时更新 Terraform，或者这个相同的命令也会安装 Terraform CLI。

![](Images/Day57_IAC1.png)

我们将进一步深入了解 HCL，然后开始使用 Terraform 在各种不同的平台上创建一些基础设施资源。

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

[第58天](day58.md)见
