---
title: '#90DaysOfDevOps - Microsoft Azure 存储模型 - 第32天'
published: false
description: 90DaysOfDevOps - Microsoft Azure 存储模型
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048775
---
## Microsoft Azure 存储模型

### 存储服务

- Azure 存储服务由存储账户提供。
- 存储账户主要通过 REST API 访问。
- 存储账户必须有一个唯一的名称，该名称是 DNS 名称的一部分：`<Storage Account name>.core.windows.net`
- 各种复制和加密选项。
- 位于资源组内

我们可以通过 Azure 门户顶部的搜索栏搜索 Storage Group（存储组）来创建我们的存储组。

![](Images/Day32_Cloud1.png)

然后我们可以按照步骤创建我们的存储账户，记住这个名称需要是唯一的，并且必须全部小写，不能有空格，但可以包含数字。

![](Images/Day32_Cloud2.png)

我们还可以选择希望对我们的存储账户以及存储在其中的任何内容使用的冗余级别。列表越往下，选项越贵，但数据的分布也越广。

即使是默认的冗余选项也为我们提供了 3 份数据副本。

[Azure 存储冗余](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy)

上面链接的摘要如下：

- **本地冗余存储（Locally-redundant storage, LRS）** - 在主区域的单个数据中心内将数据复制三次。

- **异地冗余存储（Geo-redundant storage, GRS）** - 使用 LRS 在主区域的单个物理位置内同步复制数据三次。

- **区域冗余存储（Zone-redundant storage, ZRS）** - 在主区域的三个 Azure 可用性区域之间同步复制 Azure 存储数据。

- **异地区域冗余存储（Geo-zone-redundant storage, GZRS）** - 结合了可用性区域之间冗余提供的高可用性，以及异地复制提供的区域中断保护。GZRS 存储账户中的数据跨主区域的三个 Azure 可用性区域复制，并且还会复制到第二个地理区域以防止区域灾难。

![](Images/Day32_Cloud3.png)

回到性能选项。我们有标准（Standard）和高级（Premium）可供选择。在我们的演练中选择了标准，但高级提供了一些特定选项。

![](Images/Day32_Cloud4.png)

然后在下拉菜单中，你可以看到我们有这三个选项可供选择。

![](Images/Day32_Cloud5.png)

存储账户还有很多高级选项，但目前我们不需要深入了解这些领域。这些选项涉及加密和数据保护。

### 托管磁盘（Managed Disks）

可以通过几种不同的方式实现存储访问。

通过以下方式进行身份验证访问：
- 用于完全控制的共享密钥（Shared key）。
- 用于委托的、细粒度访问的共享访问签名（Shared Access Signature, SAS）。
- Azure Active Directory（在可用的情况下）

公共访问：
- 还可以授予公共访问权限以启用匿名访问，包括通过 HTTP。
- 这方面的一个示例可能是在块 Blob 中托管基本内容和文件，以便浏览器可以查看和下载此数据。

如果你从另一个 Azure 服务访问存储，流量将保留在 Azure 内部。

在存储性能方面，我们有两种不同类型：
- **标准（Standard）** - 最大 IOPS 数
- **高级（Premium）** - 保证的 IOPS 数

在选择适合任务的存储时，还需要考虑非托管磁盘和托管磁盘之间的区别。

### 虚拟机存储

- 虚拟机操作系统磁盘通常存储在持久性存储上。
- 某些无状态工作负载不需要持久性存储，而降低延迟是更大的好处。
- 有些 VM 支持临时操作系统托管磁盘，这些磁盘创建在节点本地存储上。
  - 这些也可以与 VM 规模集（VM Scale Sets）一起使用。

托管磁盘（Managed Disks）是可与 Azure 虚拟机一起使用的持久性块存储。你可以拥有 Ultra Disk Storage、Premium SSD、Standard SSD、Standard HDD。它们还具有一些特性。

- 快照和镜像支持
- 在 SKU 之间简单移动
- 与可用性集结合时更好的可用性
- 基于磁盘大小计费，而不是基于消耗的存储计费。

## 归档存储（Archive Storage）

- **冷层（Cool Tier）** - 冷存储层可用于块 Blob（block BLOBs）和追加 Blob（append BLOBs）。
  - 较低的存储成本
  - 较高的交易成本。
- **归档层（Archive Tier）** - 归档存储可用于块 Blob。
  - 这是在每个 Blob 的基础上配置的。
  - 成本更低，数据检索延迟更长。
  - 与常规 Azure 存储具有相同的数据持久性。
  - 可以根据需要启用自定义数据分层。

### 文件共享（File Sharing）

根据上述存储账户的创建，我们现在可以创建文件共享。

![](Images/Day32_Cloud6.png)

这将在 Azure 中提供 SMB2.1 和 3.0 文件共享。

可在 Azure 内部和外部通过 SMB3 以及向互联网开放的端口 445 使用。

在 Azure 中提供共享文件存储。

除了 REST API 之外，还可以使用标准 SMB 客户端进行映射。

你可能还会注意到 [Azure NetApp Files](https://vzilla.co.uk/vzilla-blog/azure-netapp-files-how)（SMB 和 NFS）

### 缓存与媒体服务

Azure 内容分发网络（Content Delivery Network, CDN）在全球各地提供静态 Web 内容的缓存。

Azure 媒体服务（Azure Media Services）提供媒体转码技术以及播放服务。

## Microsoft Azure 数据库模型

回到 [第28天](day28.md)，我们介绍了各种服务选项。其中之一是 PaaS（Platform as a Service，平台即服务），你将大量基础设施和操作系统抽象掉，剩下的是对应用程序或本例中数据库模型的控制。

### 关系型数据库

Azure SQL Database 提供基于 Microsoft SQL Server 的关系型数据库即服务。

这是运行最新 SQL 分支的 SQL，在需要特定功能版本时可以使用数据库兼容级别。

有几种配置方式，我们可以提供一个单一数据库，它在实例中提供一个数据库，而弹性池（elastic pool）允许多个数据库共享一个容量池并集体扩展。

这些数据库实例可以像常规 SQL 实例一样访问。

还有针对 MySQL、PostgreSQL 和 MariaDB 的额外托管产品。

![](Images/Day32_Cloud7.png)

### NoSQL 解决方案

Azure Cosmos DB 是一种与方案无关（schema agnostic）的 NoSQL 实现。

99.99% SLA

全球分布式数据库，在世界各地的第 99 个百分位数上具有个位数延迟，并具有自动归属（automatic homing）功能。

分区键（Partition key）用于数据的分区/分片/分布。

支持各种数据模型（文档、键值、图、列族）

支持各种 API（DocumentDB SQL、MongoDB、Azure Table Storage 和 Gremlin）

![](Images/Day32_Cloud9.png)

基于 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem) 提供各种一致性模型。

![](Images/Day32_Cloud8.png)

### 缓存（Caching）

在不深入讨论 Redis 等缓存系统的情况下，我想提一下 Microsoft Azure 有一个名为 Azure Cache for Redis 的服务。

Azure Cache for Redis 提供基于 Redis 软件的内存数据存储。

- 它是开源 Redis 缓存的实现。
    - 一个托管的、安全的 Redis 缓存实例。
    - 提供不同的层级
    - 必须更新应用程序以利用缓存。
    - 针对读需求高于写需求的应用程序。
    - 基于键值存储。

![](Images/Day32_Cloud10.png)

我感谢过去几天关于 Microsoft Azure 的大量笔记记录和理论学习，但我想在进入这些组件如何协同工作的动手实践方面之前，先涵盖基础知识。

在我们可以启动一些基于场景的服务部署之前，我们还有一个关于网络的理论部分需要了解。我们还想看看与 Microsoft Azure 交互的一些不同方式，而不仅仅是到目前为止我们一直在使用的门户。

## 资源

- [Hybrid Cloud and MultiCloud](https://www.youtube.com/watch?v=qkj5W98Xdvw)
- [Microsoft Azure Fundamentals](https://www.youtube.com/watch?v=NKEFWyqJ5XA&list=WL&index=130&t=12s)
- [Google Cloud Digital Leader Certification Course](https://www.youtube.com/watch?v=UGRDM86MBIQ&list=WL&index=131&t=10s)
- [AWS Basics for Beginners - Full Course](https://www.youtube.com/watch?v=ulprqHHWlng&t=5352s)

[第33天](day33.md) 见