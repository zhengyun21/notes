---
title: '#90DaysOfDevOps - Microsoft Azure 基础知识 - 第29天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 基础知识
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048705
---
## Microsoft Azure 基础知识

在我们开始之前，Twitter 投票的获胜者是 Microsoft Azure，因此是本页的标题。结果很接近，而且在 24 小时内看到结果陆续出来也相当有趣。

![](Images/Day29_Cloud1.png)

我想说的是，就涵盖这个话题而言，这将让我更好地了解和更新 Microsoft Azure 上可用的服务，在日常工作中我倾向于 Amazon AWS。不过，我已经为三大云提供商准备好了资源。

我很感激还有更多云提供商，而且投票只包含了这 3 个，特别是有一些关于 Oracle Cloud 的评论。我很想听听其他云提供商在野外的使用情况。

### 基础知识

- 提供公共云服务
- 地理分布（全球 60+ 区域）
- 通过互联网和/或私有连接访问
- 多租户模型
- 基于消费的计费 -（按需付费 | 随增长付费）
- 针对不同需求的大量服务类型和产品。

- [Microsoft Azure 全球基础设施](https://infrastructuremap.microsoft.com/explore)

正如我们讨论过的 SaaS 和混合云，我们不打算在这里涵盖这些主题。

开始并跟着学习的最佳方式是点击链接，这将使你能够启动一个 [Microsoft Azure 免费账户](https://azure.microsoft.com/en-gb/free/)。

### 区域

我在上面链接了交互式地图，但我们可以在下面的图像中看到 Microsoft Azure 平台在全球提供的区域的广度。

![](Images/Day29_Cloud2.png)
*图片取自 [Microsoft Docs - 2021/01/05](https://docs.microsoft.com/en-us/azure/networking/microsoft-global-network)*

你还会看到几个"主权"云，意味着它们不与其他区域连接或通信，例如，这些将与政府相关联，例如 `AzureUSGovernment`，还有 `AzureChinaCloud` 等等。

当我们在 Microsoft Azure 中部署服务时，我们几乎会为所有东西选择一个区域。然而，需要注意的是，并非每个服务都在每个区域可用。你可以看到 [各区域可用的产品](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=all)，在我写这篇文章的时候，在 West Central US 我们无法使用 Azure Databricks。

我在上面提到了"几乎所有东西"，有一些服务是链接到区域的，例如 Azure Bot Services、Bing Speech、Azure Virtual Desktop、Static Web Apps 等等。

在幕后，一个区域可能由多个数据中心组成。这些将被称为可用区（Availability Zones）。

在下面的图像中你会看到，这再次取自 Microsoft 官方文档，它描述了一个区域是什么以及它是由可用区组成的。然而，并非所有区域都有多个可用区。

![](Images/Day29_Cloud3.png)

Microsoft 文档非常好，你可以在 [这里](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview) 阅读更多关于区域和可用区的信息。

### 订阅

请记住，我们提到 Microsoft Azure 是一种消费模型云，你会发现所有主要的云提供商都遵循这个模型。

如果你是企业，那么你可能希望或已经与 Microsoft 签订了企业协议（Enterprise Agreement），以使你的公司能够使用这些 Azure 服务。

如果你像我一样，使用 Microsoft Azure 进行学习，那么我们还有其他几个选项。

我们有 [Microsoft Azure 免费账户](https://azure.microsoft.com/en-gb/free/)，它通常会给你一些免费的云积分，可以在一段时间内用于 Azure。

还可以使用 Visual Studio 订阅，它可能会在你每年订阅 Visual Studio 的同时，每月给你一些免费积分，这在多年前通常被称为 MSDN。[Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/credit-for-visual-studio-subscribers/)

最后还有一种方式是交上信用卡，采用按需付费模式。[按需付费](https://azure.microsoft.com/en-us/pricing/purchase-options/pay-as-you-go/)

订阅可以被看作是不同的订阅之间的边界，可能是成本中心，但完全是不同的环境。订阅是创建资源的地方。

### 管理组

管理组使我们能够在我们的 Azure AD 或租户环境中跨订阅隔离控制。管理组允许我们控制策略、RBAC（基于角色的访问控制）和预算。

订阅属于这些管理组，所以你的 Azure AD 租户中可以有许多订阅，这些订阅然后也可以控制策略、RBAC 和预算。

### 资源管理器和资源组

**Azure Resource Manager（Azure 资源管理器）**
- 基于 JSON 的 API，构建在资源提供程序之上。
- 资源属于一个资源组，并共享一个共同的生命周期。
- 并行性
- 基于 JSON 的部署是声明式的、幂等的，并理解资源之间的依赖关系，以控制创建和顺序。

**资源组**
- 每个 Azure Resource Manager 资源都只存在于一个资源组中！
- 资源组创建在一个区域中，但可以包含来自该区域之外的资源。
- 资源可以在资源组之间移动
- 资源组与其他资源组之间不是隔离的，资源组之间可以进行通信。
- 资源组也可以控制策略、RBAC 和预算。

### 动手实践

让我们去连接并确保我们有一个**订阅**可用。我们可以检查我们开箱即用的简单**管理组**，然后我们可以去在我们首选的**区域**中创建一个新的专用**资源组**。

当我们首次登录到我们的 [Azure 门户](https://portal.azure.com/#home) 时，你会在顶部看到搜索资源、服务和文档的能力。

![](Images/Day29_Cloud4.png)

我们首先要查看我们的订阅，你会看到我在这里使用的是 Visual Studio Professional 订阅，它每月给我一些免费积分。

![](Images/Day29_Cloud5.png)

如果我们进入其中，你会获得更广泛的视图，并了解订阅正在发生什么或可以用订阅做什么，我们可以在左侧看到计费信息以及控制功能，你可以在其中定义 IAM 访问控制，再往下还有更多可用的资源。

![](Images/Day29_Cloud6.png)

可能有一种情况是你有多个订阅，并且你想将它们全部管理在一个下，这就是管理组可以用来隔离责任组的地方。在我的下面，你可以看到只有我的租户根组与我的订阅。

你还会在上一个图像中看到，父管理组与租户根组上使用的 ID 相同。

![](Images/Day29_Cloud7.png)

接下来我们有资源组，这是我们组合资源的地方，我们可以轻松地将它们管理在一个地方。我已经为各种其他项目创建了一些。

![](Images/Day29_Cloud8.png)

鉴于我们在接下来的几天要做的事情，我们想创建我们的资源组。在这个控制台中很容易完成，点击上一个图像上的创建选项。

![](Images/Day29_Cloud9.png)

会进行一个验证步骤，然后你有机会审查你的创建，然后创建。你还会在底部看到"下载用于自动化的模板"，这允许我们获取 JSON 格式，以便以后如果我们想要，可以以自动化的方式执行这个简单的操作，我们稍后也会介绍这个。

![](Images/Day29_Cloud10.png)

点击创建，然后在我们的资源组列表中，我们现在有了我们的 "90DaysOfDevOps" 组，为我们在下一节中所做的事情做好了准备。

![](Images/Day29_Cloud11.png)

## 相关资料

- [混合云和多云](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure 基础知识](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader 认证课程](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS 初学者基础 - 完整课程](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

[第30天](day30.md)见
