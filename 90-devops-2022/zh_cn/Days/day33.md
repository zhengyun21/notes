---
title: '#90DaysOfDevOps - Microsoft Azure 网络模型 + Azure 管理 - 第33天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 网络模型 + Azure 管理
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048706
---
## Microsoft Azure 网络模型 + Azure 管理

今天标志着 Microsoft Azure 的周年纪念日，是它 12 岁生日！（2022 年 2 月 1 日）不管怎样，我们将介绍 Microsoft Azure 中的网络模型以及 Azure 的一些管理选项。到目前为止，我们只使用了 Azure 门户，但我们提到了其他可用于在平台中驱动和创建资源的区域。

## Azure 网络模型

### 虚拟网络（Virtual Networks）

- 虚拟网络是在 Azure 中创建的构造。
- 虚拟网络分配有一个或多个 IP 范围。
- 虚拟网络位于某个区域中的订阅内。
- 在虚拟网络中创建虚拟子网以划分网络范围。
- 虚拟机放置在虚拟子网中。
- 虚拟网络内的所有虚拟机都可以通信。
- 每个虚拟网络有 65,536 个私有 IP。
- 只需为从区域流出的流量（离开区域的数据）付费。
- 支持 IPv4 和 IPv6。
  - IPv6 用于面向公众的网络和虚拟网络内部。

我们可以将 Azure 虚拟网络类比为 AWS VPC。但是，有一些差异需要注意：

- 在 AWS 中，会创建一个默认 VNet，而在 Microsoft Azure 中则不是这样，你必须根据自己的需求创建第一个虚拟网络。
- Azure 中的所有虚拟机默认都具有 NAT 互联网访问权限。没有 AWS 中的 NAT 网关。
- 在 Microsoft Azure 中，没有私有或公共子网的概念。
- 公共 IP 是一种可以分配给 vNIC 或负载均衡器的资源。
- 虚拟网络和子网有自己的 ACL，支持子网级别的委派。
- 子网跨可用性区域，而在 AWS 中，每个可用性区域都有子网。

我们还有虚拟网络对等互联（Virtual Network Peering）。这使跨租户和区域的虚拟网络能够使用 Azure 主干网连接。它不是传递性的，但可以通过中心虚拟网络中的 Azure 防火墙启用。使用网关传输允许对等互联的虚拟网络使用连接的网络的连接性，例如通过 ExpressRoute 连接到本地。

### 访问控制（Access Control）

- Azure 利用网络安全组（Network Security Groups, NSG），这些是有状态的。
- 启用规则创建，然后分配到网络安全组
- 网络安全组应用于子网或 VM。
- 当应用于子网时，它仍然在虚拟机 NIC 上强制执行，它不是"边缘"设备。

![](Images/Day33_Cloud1.png)

- 规则在网络安全组中组合。
- 基于优先级，可以实现灵活的配置。
- 优先级数字越低意味着优先级越高。
- 大多数逻辑是基于 IP 地址构建的，但也可以使用一些标签和标签。

| 描述      | 优先级 |   源地址   | 源端口 | 目标地址 | 目标端口 | 操作 |
| -----------      | ---------|   --------------   | ----------- | ------------------- | ---------------- | ------ |
| Inbound 443      | 1005     | *                  | *           | *                   | 443              | Allow  |
| ILB              | 1010     | Azure LoadBalancer | *           | *                   | 10000            | Allow  |
| Deny All Inbound | 4000     | *                  | *           | *                   | *                | DENY   |

我们还有应用程序安全组（Application Security Groups, ASGs）

- NSG 专注于 IP 地址范围，这对于不断增长的环境可能难以维护。
- ASG 支持为不同的应用程序角色定义真实名称（别名）（Web 服务器、数据库服务器、WebApp1 等）。
- 虚拟机 NIC 成为一个或多个 ASG 的成员。

然后，ASG 可以用于作为网络安全组一部分的规则中，以控制通信流，并且仍然可以使用 NSG 功能（如服务标签）。

| 操作| 名称               | 源     | 目标 | 端口         |
| ------| ------------------ | ---------- | ----------- | ------------ |
| Allow | AllowInternettoWeb | Internet   | WebServers  | 443(HTTPS)   |
| Allow | AllowWebToApp      | WebServers | AppServers  | 443(HTTPS)   |
| Allow | AllowAppToDB       | AppServers | DbServers   | 1443 (MSSQL) |
| Deny  | DenyAllinbound     | Any        | Any         | Any          |

### 负载均衡（Load Balancing）

Microsoft Azure 有两个独立的负载均衡解决方案。（第一方，Azure 市场中还有第三方可用。）两者都可以与面向外部或面向内部的端点一起运行。

- 负载均衡器（Load Balancer，第 4 层）支持基于哈希的分布和端口转发。
- 应用程序网关（App Gateway，第 7 层）支持 SSL 卸载、基于 cookie 的会话亲和力和基于 URL 的内容路由等功能。

此外，使用应用程序网关时，你还可以选择使用 Web 应用程序防火墙组件。

## Azure 管理工具

我们花了大部分理论时间浏览 Azure 门户，我建议，当涉及到遵循 DevOps 文化和流程时，许多这些任务（特别是围绕配置）将通过 API 或命令行工具完成。我想介绍一些我们可用的其他管理工具，因为我们需要了解这些工具，以便在自动化配置 Azure 环境时使用。

### Azure 门户（Azure Portal）

Microsoft Azure 门户是一个基于 Web 的控制台，提供了命令行工具的替代方案。你可以在 Azure 门户中管理订阅。从一个简单的 Web 应用到复杂的云部署，构建、管理、监控一切。你在门户中还会发现的另一件事是这些面包屑导航，正如之前提到的，JSON 是所有 Azure 资源的基础，你可能从门户开始了解功能、服务和功能，但随后了解底层的 JSON 以将其整合到你的自动化工作流中。

![](Images/Day33_Cloud2.png)

还有 Azure 预览门户（Azure Preview portal），可用于查看和测试新的和即将推出的服务及增强功能。

![](Images/Day33_Cloud3.png)

### PowerShell

在介绍 Azure PowerShell 之前，有必要先介绍一下 PowerShell。PowerShell 是一个任务自动化和配置管理框架、命令行 shell 和脚本语言。我们可能会（我敢说吗？）将其类比为我们在 Linux 部分介绍的 shell 脚本。PowerShell 最初主要出现在 Windows 操作系统上，但现在它是跨平台的。

Azure PowerShell 是一组 cmdlet，用于直接从 PowerShell 命令行管理 Azure 资源。

从下面我们可以看到，你可以使用 PowerShell 命令 `Connect-AzAccount` 连接到你的订阅

![](Images/Day33_Cloud4.png)

然后，如果我们想找到一些与 Azure VM 相关的特定命令，我们可以运行以下命令。你可以花几个小时学习和理解这种 PowerShell 编程语言。

![](Images/Day33_Cloud5.png)

Microsoft 有一些很棒的关于 PowerShell 入门和配置服务的快速入门指南 [点击这里](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azps-7.1.0)

### Visual Studio Code

像许多人一样，正如你们都看到的，我的首选 IDE 是 Visual Studio Code。

Visual Studio Code 是 Microsoft 为 Windows、Linux 和 macOS 制作的免费源代码编辑器。

从下面你可以看到，Visual Studio Code 中有许多集成和工具可用于与 Microsoft Azure 及其中的服务交互。

![](Images/Day33_Cloud6.png)

### Cloud Shell（云 shell）

Azure Cloud Shell 是一个交互式、经过身份验证、可通过浏览器访问的 shell，用于管理 Azure 资源。它提供了选择最适合你工作方式的 shell 体验的灵活性。

![](Images/Day33_Cloud7.png)

从下面可以看到，当我们在门户中首次启动 Cloud Shell 时，我们可以在 Bash 和 PowerShell 之间进行选择。

![](Images/Day33_Cloud8.png)

要使用 Cloud Shell，你必须在订阅中提供一些存储。

当你选择使用 Cloud Shell 时，它正在启动一台机器，这些机器是临时的，但你的文件通过两种方式持久化：通过磁盘镜像和挂载的文件共享。

![](Images/Day33_Cloud9.png)

- Cloud Shell 在按会话、按用户提供的临时主机上运行
- Cloud Shell 在没有交互活动 20 分钟后超时
- Cloud Shell 需要挂载 Azure 文件共享
- Cloud Shell 对 Bash 和 PowerShell 使用相同的 Azure 文件共享
- Cloud Shell 为每个用户账户分配一台机器
- Cloud Shell 使用文件共享中保存的 5 GB 镜像持久化 $HOME
- 权限在 Bash 中设置为常规 Linux 用户

以上内容复制自 [Cloud Shell 概述](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)

### Azure CLI

最后，我想介绍 Azure CLI，Azure CLI 可以安装在 Windows、Linux 和 macOS 上。安装后，你可以键入 `az`，后跟其他命令来创建、更新、删除和查看 Azure 资源。

当我最初开始学习 Azure 时，我对 Azure PowerShell 和 Azure CLI 并存感到有些困惑。

我也希望从社区获得一些关于这方面的反馈。但我的看法是，Azure PowerShell 是添加到 Windows PowerShell 或 PowerShell Core 的模块（也可在其他操作系统上使用，但并非全部），而 Azure CLI 是一个跨平台的命令行程序，连接到 Azure 并执行这些命令。

这两个选项有不同的语法，尽管据我所见和我所做过的，它们可以完成非常相似的任务。

例如，从 PowerShell 创建虚拟机将使用 `New-AzVM` cmdlet，而 Azure CLI 将使用 `az VM create`。

你之前看到我的系统上安装了 Azure PowerShell 模块，但我还安装了 Azure CLI，可以通过我 Windows 机器上的 PowerShell 调用它。

![](Images/Day33_Cloud10.png)

这里的关键要点，正如我们之前提到的，是关于选择合适的工具。Azure 依靠自动化运行。你在门户内采取的每个操作都会在某个地方转化为执行以读取、创建、修改或删除资源的代码。

Azure CLI

- 跨平台命令行界面，可安装在 Windows、macOS、Linux 上
- 在 Windows PowerShell、Cmd 或 Bash 以及其他 Unix shell 中运行。

Azure PowerShell

- 跨平台 PowerShell 模块，在 Windows、macOS、Linux 上运行
- 需要 Windows PowerShell 或 PowerShell

如果你由于某种原因无法在你的环境中使用 PowerShell，但你可以使用 .md 或 bash，那么 Azure CLI 将是你的选择。

接下来，我们将运用我们经历过的所有理论，创建一些场景并在 Azure 中进行动手实践。

## 资源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

[第34天](day34.md) 见