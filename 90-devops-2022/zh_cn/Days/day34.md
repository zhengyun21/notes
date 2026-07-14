---
title: '#90DaysOfDevOps - Microsoft Azure 动手实践场景 - 第34天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 动手实践场景
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048763
---
## Microsoft Azure 动手实践场景

过去 6 天一直专注于 Microsoft Azure 和公有云，其中很多基础内容必须包含大量理论，以了解 Azure 的构建块，但这也将很好地转化为其他主要云提供商。

我在一开始提到要获取公有云的基础知识，并选择至少一个提供商开始，如果你在不同的云之间来回切换，我相信你很容易迷失方向，而选择其中一个可以让你了解基础知识，当你掌握了这些基础知识后，跳入其他云并加速你的学习是相当容易的。

在最后一节中，我将从这个页面挑选我的动手实践场景，这是 Microsoft 创建的参考资料，用于准备 [AZ-104 Microsoft Azure 管理员](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/)

这里有一些我们到目前为止还没有详细介绍的内容，例如容器和 Kubernetes，所以我不想现在就跳进去。

在之前的文章中，我们已经创建了模块 1、2 和 3 的大部分内容。

### 虚拟网络
按照 [模块 04](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_04-Implement_Virtual_Networking.html)：

我完成了上述内容，并为 #90DaysOfDevOps 的目的更改了一些命名。我也没有使用 Cloud Shell，而是使用前几天创建的新用户在我的 Windows 机器上通过 Azure CLI 登录。

你可以使用 `az login`，这将打开一个浏览器并让你认证到你的账户。

然后我创建了一个 PowerShell 脚本和一些来自模块的参考，用于构建下面的一些任务。你可以在以下文件夹中找到相关文件。
(Cloud\01VirtualNetworking)

请确保更改脚本中的文件位置以适合你的环境。

在第一阶段，我们的环境中没有虚拟网络或虚拟机，我的资源组中只有一个 cloudshell 存储位置配置。

我首先运行我的 [PowerShell 脚本](Cloud/01VirtualNetworking/Module4_90DaysOfDevOps.ps1)

 ![](Images/Day34_Cloud1.png)

- 任务 1：创建和配置虚拟网络

 ![](Images/Day34_Cloud2.png)

- 任务 2：将虚拟机部署到虚拟网络中

 ![](Images/Day34_Cloud3.png)

- 任务 3：配置 Azure VM 的私有和公共 IP 地址

 ![](Images/Day34_Cloud4.png)

- 任务 4：配置网络安全组

![](Images/Day34_Cloud5.png)
![](Images/Day34_Cloud6.png)

- 任务 5：配置 Azure DNS 进行内部名称解析

![](Images/Day34_Cloud7.png)
![](Images/Day34_Cloud8.png)

### 网络流量管理
按照 [模块 06](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_06-Implement_Network_Traffic_Management.html)：

接下来的演练，从上一次开始，我们已经进入资源组并删除了资源，如果你没有像我一样设置用户账户以仅有权访问那一个资源组，你可以按照模块将名称更改为 `90Days*`，这将删除所有资源和资源组。这将是我对每个后续实验室的流程。

对于此实验室，我还创建了一个 PowerShell 脚本和一些来自模块的参考，用于构建下面的一些任务。你可以在以下文件夹中找到相关文件。
(Cloud\02TrafficManagement)


- 任务 1：配置实验室环境

我首先运行我的 [PowerShell 脚本](Cloud/02TrafficManagement/Mod06_90DaysOfDevOps.ps1)

![](Images/Day34_Cloud9.png)

- 任务 2：配置中心辐射型网络拓扑

![](Images/Day34_Cloud10.png)

- 任务 3：测试虚拟网络对等互联的传递性

为此，我的 90DaysOfDevOps 组没有访问 Network Watcher 的权限，因为权限问题，我预计这是因为 Network Watcher 是那些不绑定到资源组的资源之一，而我们的 RBAC 是针对此用户的资源组覆盖的。我向 90DaysOfDevOps 组添加了 East US Network Watcher 参与者角色。

![](Images/Day34_Cloud11.png)
![](Images/Day34_Cloud12.png)
![](Images/Day34_Cloud13.png)

^ 这是预期的，因为两个辐射虚拟网络没有相互对等互联（虚拟网络对等互联不是传递性的）。

- 任务 4：在中心辐射型拓扑中配置路由

我在这里遇到了另一个问题，我的账户无法在 90DaysOfDevOps 组内的 VM 中运行脚本，我不确定原因，所以我跳回了我的主管理员账户。90DaysOfDevOps 组是 90DaysOfDevOps 资源组中所有内容的所有者，所以我很想知道为什么我无法在 VM 中运行命令？

![](Images/Day34_Cloud14.png)
![](Images/Day34_Cloud15.png)

然后我能够回到我的 michael.cade@90DaysOfDevOps.com 账户并继续本节。在这里，我们再次运行相同的测试，但现在结果是可达的。

![](Images/Day34_Cloud16.png)

- 任务 5：实现 Azure 负载均衡器

![](Images/Day34_Cloud17.png)
![](Images/Day34_Cloud18.png)

- 任务 6：实现 Azure 应用程序网关

![](Images/Day34_Cloud19.png)
![](Images/Day34_Cloud20.png)

### Azure 存储
按照 [模块 07](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_07-Manage_Azure_Storage.html)：

对于此实验室，我还创建了一个 PowerShell 脚本和一些来自模块的参考，用于构建下面的一些任务。你可以在以下文件夹中找到相关文件。
(Cloud\03Storage)

- 任务 1：配置实验室环境

我首先运行我的 [PowerShell 脚本](Cloud/03Storage/Mod07_90DaysOfDeveOps.ps1)

![](Images/Day34_Cloud21.png)

- 任务 2：创建和配置 Azure 存储账户

![](Images/Day34_Cloud22.png)

- 任务 3：管理 Blob 存储

![](Images/Day34_Cloud23.png)

- 任务 4：管理 Azure 存储的身份验证和授权

![](Images/Day34_Cloud24.png)
![](Images/Day34_Cloud25.png)

我有点不耐烦等待这个被允许，但它最终确实奏效了。

![](Images/Day34_Cloud26.png)


- 任务 5：创建和配置 Azure 文件共享

在运行命令时，这无法与 michael.cade@90DaysOfDevOps.com 一起工作，所以我使用了我的提升权限账户。

![](Images/Day34_Cloud27.png)
![](Images/Day34_Cloud28.png)
![](Images/Day34_Cloud29.png)


- 任务 6：管理 Azure 存储的网络访问

![](Images/Day34_Cloud30.png)

### 无服务器（实现 Web 应用）
按照 [模块 09a](https://microsoftlearning.github.io/AZ-104-MicrosoftAzureAdministrator/Instructions/Labs/LAB_09a-Implement_Web_Apps.html)：


- 任务 1：创建 Azure Web 应用

![](Images/Day34_Cloud31.png)

- 任务 2：创建暂存部署槽

![](Images/Day34_Cloud34.png)

- 任务 3：配置 Web 应用部署设置

![](Images/Day34_Cloud33.png)

- 任务 4：将代码部署到暂存部署槽

![](Images/Day34_Cloud32.png)

- 任务 5：交换暂存槽

![](Images/Day34_Cloud35.png)

- 任务 6：配置和测试 Azure Web 应用的自动扩展

我使用的脚本可以在 (Cloud/05Serverless) 中找到

![](Images/Day34_Cloud36.png)

这总结了 Microsoft Azure 和公有云的一般部分。我要说，我在攻克和完成这些场景时获得了很多乐趣。

## 资源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

接下来，我们将深入探讨版本控制系统，特别是 git，然后是代码仓库概述，我们将选择 GitHub，因为这是我的首选。

[第35天](day35.md) 见