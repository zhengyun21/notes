---
title: '#90DaysOfDevOps - Microsoft Azure 安全模型 - 第30天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 安全模型
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049039
---
## Microsoft Azure 安全模型

继 Microsoft Azure 概述之后，我们将从 Azure 安全开始，看看它在我们的日常工作中能提供哪些帮助。在大多数情况下，我发现内置角色已经足够，但了解到我们可以创建和使用许多不同的身份验证和配置领域。与其他公有云相比，我发现 Microsoft Azure 凭借其 Active Directory 背景在安全方面相当先进。

## Microsoft Azure 安全模型

这是 Microsoft Azure 与其他公有云提供商明显不同的一个领域，在 Azure 中，始终都有 Azure AD。

### 目录服务

- Azure Active Directory（Azure AD，Azure 活动目录）托管 Microsoft Azure 和其他 Microsoft 云服务使用的安全主体。
- 身份验证通过 SAML、WS-Federation、OpenID Connect 和 OAuth2 等协议完成。
- 查询通过名为 Microsoft Graph API 的 REST API 完成。
- 租户有一个 tenant.onmicrosoft.com 默认名称，但也可以拥有自定义域名。
- 订阅与 Azure Active Directory 租户相关联。

如果我们将 AWS 与之对比，等效的产品应该是 AWS IAM（Identity & Access Management，身份与访问管理），尽管两者仍然非常不同。

Azure AD Connect 提供了从 AD 复制账户到 Azure AD 的能力。这还可以包括组，有时还包括对象。这可以是细粒度的，并且支持过滤。支持多林和多域。

可以在 Microsoft Azure Active Directory（AD）中创建云账户，但大多数组织已经在其本地 Active Directory 中为其用户创建了账户。

Azure AD Connect 还允许你不仅查看 Windows AD 服务器，还可以查看其他 Azure AD、Google 等。这也提供了与外部人员和组织协作的能力，这被称为 Azure B2B。

Active Directory Domain Services（AD DS，Active Directory 域服务）与 Microsoft Azure Active Directory 之间的身份验证选项可以通过密码哈希同步身份来实现。

![](Images/Day30_Cloud1.png)

密码哈希的传递是可选的，如果不使用，则需要传递身份验证（Pass-through authentication）。

下面链接的视频详细介绍了传递身份验证。

[使用 Azure Active Directory 传递身份验证的用户登录](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-pta)

![](Images/Day30_Cloud2.png)

### 联合身份验证（Federation）

可以公平地说，如果你正在使用 Microsoft 365、Microsoft Dynamics 和本地 Active Directory，那么将其集成到 Azure AD 进行联合身份验证是相当容易理解和实现的。然而，你可能正在使用 Microsoft 生态系统之外的其他服务。

Azure AD 可以作为这些其他非 Microsoft 应用程序和其他目录服务的联合身份验证代理。

这将在 Azure 门户中显示为企业应用程序（Enterprise Applications），其中有大量可选应用。

![](Images/Day30_Cloud3.png)

如果你在企业应用程序页面向下滚动，你会看到一长串特色应用程序。

![](Images/Day30_Cloud4.png)

此选项还允许"自带"集成，即你正在开发的应用程序或非库应用程序。

我以前没有研究过这个，但我可以看到，与其他云提供商和功能相比，这是一套相当强大的功能集。

### 基于角色的访问控制（Role-Based Access Control, RBAC）

我们在 [第29天](day29.md) 已经介绍过这里的范围，我们可以根据以下某个区域设置基于角色的访问控制：

- 订阅（Subscriptions）
- 管理组（Management Group）
- 资源组（Resource Group）
- 资源（Resources）

角色可以分为三类，Microsoft Azure 中有许多内置角色。这三类是：

- 所有者（Owner）
- 参与者（Contributor）
- 读者（Reader）

所有者和参与者在范围边界上非常相似，但所有者可以更改权限。

其他角色特定于某些类型的 Azure 资源，以及自定义角色。

我们应该重点将权限分配给组而不是用户。

权限是继承的。

如果我们回到查看创建的 "90DaysOfDevOps" 资源组，并在其中检查访问控制（IAM），你可以看到我们有一列表参与者和一个自定义用户访问管理员，我们也有所有者列表（但我不能展示这个）。

![](Images/Day30_Cloud5.png)

我们还可以在这里检查我们分配的角色，它们是内置角色（BuiltInRoles），以及它们属于哪个类别。

![](Images/Day30_Cloud6.png)

如果我们想针对此资源组检查某个账户，并确保我们希望拥有该访问权限的账户具有正确的权限，或者我们想检查某个用户是否有过多访问权限，我们也可以使用"检查访问"选项卡。

![](Images/Day30_Cloud7.png)

### Microsoft Defender for Cloud

- Microsoft Defender for Cloud（以前称为 Azure Security Center，Azure 安全中心）提供了对整个 Azure 环境安全状况的洞察。

- 一个单一的仪表板，可以查看所有 Azure 和非 Azure 资源（通过 Azure Arc）的整体安全健康状况以及安全加固指导。

- 免费层包括持续评估和安全建议。

- 付费计划用于受保护的资源类型（例如服务器、AppService、SQL、存储、容器、KeyVault）。

我切换到了另一个订阅来查看 Azure 安全中心，你可以在这里看到，基于非常少的资源，我在一个地方有一些建议。

![](Images/Day30_Cloud8.png)

### Azure Policy（Azure 策略）

- Azure Policy 是 Azure 原生服务，有助于在规模上强制执行组织标准并评估合规性。

- 集成到 Microsoft Defender for Cloud 中。Azure Policy 审核不合规的资源并应用修正。

- 通常用于管理资源一致性、法规合规性、安全性、成本和管理标准。

- 使用 JSON 格式存储评估逻辑，以确定资源是否合规，以及对不合规采取的任何操作（例如审核、AuditIfNotExists、拒绝、修改、DeployIfNotExists）。

- 免费使用。例外是 Azure Arc 连接的资源按服务器/月收费，用于 Azure Policy 来宾配置（Guest Configuration）使用。

### 动手实践

我购买了 www.90DaysOfDevOps.com 域名，我想将它添加到我的 Azure Active Directory 门户中，[使用 Azure Active Directory 门户添加自定义域名](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/add-custom-domain)

![](Images/Day30_Cloud9.png)

这样我们就可以在新的 Active Directory 域上创建一个新用户。

![](Images/Day30_Cloud10.png)

现在，我们想为所有新的 90DaysOfDevOps 用户创建一个组。我们可以按照下面的方式创建一个组，注意我使用的是"动态用户"（Dynamic User），这意味着 Azure AD 将查询用户账户并动态添加他们，而不是"已分配"（Assigned），后者需要手动将用户添加到组中。

![](Images/Day30_Cloud11.png)

创建查询时有很多选项，我的计划是简单地查找主体名称，并确保名称包含 @90DaysOfDevOps.com。

![](Images/Day30_Cloud12.png)

现在，因为我们已经为 michael.cade@90DaysOfDevOps.com 创建了用户账户，我们可以验证规则是否有效。作为对比，我还添加了另一个与另一个域关联的账户，你可以看到，由于这个规则，我们的用户不会进入这个组。

![](Images/Day30_Cloud13.png)

我随后添加了新的 user1@90DaysOfDevOps.com，如果我们去检查组，可以看到我们的成员。

![](Images/Day30_Cloud14.png)

如果我们有 x100 个这样的需求，我们不希望在控制台中完成所有这些操作，我们将希望利用批量选项来创建、邀请、删除用户，或者你将希望研究 PowerShell 来实现这种自动化的扩展方法。

现在我们可以进入我们的资源组，并指定在 90DaysOfDevOps 资源组上，我们希望所有者是我们刚刚创建的组。

![](Images/Day30_Cloud15.png)

我们也可以在这里进入并拒绝分配对我们的资源组的访问。

现在，如果我们使用新用户账户登录 Azure 门户，你可以看到我们只有权访问我们的 90DaysOfDevOps 资源组，而不是之前图片中看到的其他资源组，因为我们没有访问权限。

![](Images/Day30_Cloud16.png)

以上内容很好，如果这是有权访问 Azure 门户内资源的用户，但并非每个用户都需要了解门户，但为了检查访问权限，我们可以使用 [Apps Portal](https://myapps.microsoft.com/)，这是一个供我们测试的单点登录门户。

![](Images/Day30_Cloud17.png)

你可以使用自己的品牌定制此门户，这可能是我们以后会遇到的内容。

## 资源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

[第31天](day31.md) 见