---
title: "#90DaysOfDevOps - 数据服务 - 第85天"
published: false
description: "90DaysOfDevOps - 数据服务"
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048781
---
## 数据服务

数据库是我们在环境中最常遇到的数据服务。我想利用本节课程来探索一些不同类型的数据库以及它们各自的使用场景。其中一些我们在整个挑战过程中已经使用和见过。

从应用开发的角度来看，选择正确的数据服务或数据库对于应用程序的性能和可扩展性来说将是一个至关重要的决定。

https://www.youtube.com/watch?v=W2Z7fbCLSTw

### 键值存储（Key-value）

键值数据库是一种非关系型数据库，使用简单的键值方法来存储数据。键值数据库将数据存储为键值对的集合，其中键作为唯一标识符。键和值可以是任何东西，从简单对象到复杂的复合对象。键值数据库具有高度的可分区性，能够实现其他类型数据库无法达到的水平扩展规模。

键值数据库的一个例子是 Redis。

*Redis 是一个内存数据结构存储，用作分布式内存键值数据库、缓存和消息代理，具有可选的持久化功能。Redis 支持多种抽象数据结构，如字符串、列表、映射、集合、有序集合、HyperLogLogs、位图、流和空间索引。*

![](Images/Day85_Data1.png)

从 Redis 的描述中可以看出，这意味着我们的数据库速度很快，但作为权衡，我们的空间有限。此外，没有查询或连接功能，这意味着数据建模选项非常有限。

最适用于：
- 缓存（Caching）
- 发布/订阅（Pub/Sub）
- 排行榜（Leaderboards）
- 购物车

通常用作另一个持久化数据层之上的缓存。

### 宽列存储（Wide Column）

宽列数据库是一种 NoSQL 数据库，将数据存储组织到灵活的列中，这些列可以分布在多个服务器或数据库节点上，使用多维映射通过列、行和时间戳来引用数据。

*Cassandra 是一个免费开源的分布式宽列存储 NoSQL 数据库管理系统，旨在处理大量数据跨许多商用服务器，提供高可用性且无单点故障。*

![](Images/Day85_Data2.png)

没有模式（Schema），这意味着可以处理非结构化数据，然而这可以被视为某些工作负载的优势。

最适用于：
- 时序数据（Time-Series）
- 历史记录（Historical Records）
- 高写入、低读取（High-Write, Low-Read）

### 文档数据库（Document）

文档数据库（也称为面向文档的数据库或文档存储）是一种在文档中存储信息的数据库。

*MongoDB 是一个源代码可用的跨平台面向文档的数据库程序。被归类为 NoSQL 数据库程序，MongoDB 使用类似 JSON 的文档和可选的模式。MongoDB 由 MongoDB Inc. 开发，并在服务器端公共许可证（Server Side Public License）下授权。*

![](Images/Day85_Data3.png)

NoSQL 文档数据库允许企业无需使用复杂的 SQL 代码即可存储简单数据。快速存储，同时不损害可靠性。

最适用于：

- 大多数应用程序
- 游戏
- 物联网（Internet of Things）

### 关系型数据库（Relational）

如果你是数据库新手但你听说过数据库，我猜你一定接触过关系型数据库。

关系型数据库是基于 E. F. Codd 于 1970 年提出的关系数据模型的数字数据库。用于维护关系型数据库的系统是关系型数据库管理系统（RDBMS）。许多关系型数据库系统都有使用 SQL 进行查询和维护数据库的选项。

*MySQL 是一个开源的关系型数据库管理系统。它的名字是 "My"（联合创始人 Michael Widenius 的女儿的名字）和 "SQL"（Structured Query Language，结构化查询语言的缩写）的组合。*

MySQL 是关系型数据库的一个例子，还有许多其他选择。

![](Images/Day85_Data4.png)

在研究关系型数据库时，术语或缩写 **ACID** 被频繁提及，（原子性 Atomicity、一致性 Consistency、隔离性 Isolation、持久性 Durability）是数据库事务的一组属性，旨在保证数据的有效性，即使有错误、电源故障和其他意外情况。在数据库的上下文中，满足 ACID 属性的一系列数据库操作（可以被视为对数据的单一逻辑操作）被称为事务。例如，从一个银行账户向另一个账户转账，即使涉及多个更改（如借记一个账户和贷记另一个账户），也是一个单一的事务。

最适用于：
- 大多数应用程序（它已经存在多年，但这并不意味着它是最好的）

对于非结构化数据或扩展能力来说，它不是理想的选择，而这正是其他一些 NoSQL 提及的方案在某些工作负载下提供更好的扩展能力的地方。

### 图数据库（Graph）

图数据库存储节点和关系，而不是表或文档。数据的存储方式就像你在白板上勾勒想法一样。你的数据被存储时不受限于预定义的模型，允许以一种非常灵活的方式来思考和使用数据。

*Neo4j 是由 Neo4j, Inc. 开发的图数据库管理系统。被其开发者描述为具有原生图存储和处理的 ACID 兼容事务数据库。*

最适用于：

- 图（Graphs）
- 知识图谱（Knowledge Graphs）
- 推荐引擎（Recommendation Engines）

### 搜索引擎（Search Engine）

在上一节中，我们实际上使用了一种搜索引擎数据库，即 Elasticsearch。

搜索引擎数据库是一种非关系型数据库，专门用于数据内容的搜索。搜索引擎数据库使用索引来对数据的相似特征进行分类，并促进搜索能力。

*Elasticsearch 是一个基于 Lucene 库的搜索引擎。它提供了一个分布式的、多租户 capable 的全文搜索引擎，具有 HTTP Web 接口和无模式的 JSON 文档。*

最适用于：

- 搜索引擎
- 自动补全（Typeahead）
- 日志搜索

### 多模型数据库（Multi-model）

多模型数据库是一种数据库管理系统，旨在支持针对单一集成后端的多种数据模型。相比之下，大多数数据库管理系统围绕单一数据模型组织，该数据模型决定了数据如何组织、存储和操作。文档、图、关系和键值模型是多模型数据库可能支持的数据模型的例子。

*Fauna 是一个灵活、开发者友好的事务数据库，作为安全且可扩展的云 API 交付，具有原生 GraphQL 支持。*

最适用于：

- 你不受限于必须选择一种数据模型
- ACID 兼容
- 快速
- 无配置开销
- 你想要如何消费数据，让云来完成繁重的工作

以上就是这次数据库概览课程的内容，无论你身处哪个行业，你都会遇到数据库相关的领域。接下来，我们将以其中一些例子为例，研究数据管理，特别是这些数据服务的保护和存储。稍后在本节中将会涉及。

下面我链接了大量的资源，说实话，你可能需要花费 90 年的时间来深入研究所有数据库类型以及随之而来的一切。

## 相关资料

- [Redis Crash Course - the What, Why and How to use Redis as your primary database](https://www.youtube.com/watch?v=OqCK95AS-YE)
- [Redis: How to setup a cluster - for beginners](https://www.youtube.com/watch?v=GEg7s3i6Jak)
- [Redis on Kubernetes for beginners](https://www.youtube.com/watch?v=JmCn7k0PlV4)
- [Intro to Cassandra - Cassandra Fundamentals](https://www.youtube.com/watch?v=YjYWsN1vek8)
- [MongoDB Crash Course](https://www.youtube.com/watch?v=ofme2o29ngU)
- [MongoDB in 100 Seconds](https://www.youtube.com/watch?v=-bt_y4Loofg)
- [What is a Relational Database?](https://www.youtube.com/watch?v=OqjJjpjDRLc)
- [Learn PostgreSQL Tutorial - Full Course for Beginners](https://www.youtube.com/watch?v=qw--VYLpxG4)
- [MySQL Tutorial for Beginners [Full Course]](https://www.youtube.com/watch?v=7S_tz1z_5bA)
- [What is a graph database? (in 10 minutes)](https://www.youtube.com/watch?v=REVkXVxvMQE)
- [What is Elasticsearch?](https://www.youtube.com/watch?v=ZP0NmfyfsoM)
- [FaunaDB Basics - The Database of your Dreams](https://www.youtube.com/watch?v=2CipVwISumA)
- [Fauna Crash Course - Covering the Basics](https://www.youtube.com/watch?v=ihaB7CqJju0)


[第86天](day86.md)见。
