---
title: '#90DaysOfDevOps - 全景图：配置管理 - 第63天'
published: false
description: 90DaysOfDevOps - 全景图：配置管理
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048711
---
## 全景图：配置管理

紧接基础设施即代码（Infrastructure as Code）这一节之后，当我们谈论配置管理或应用程序配置管理时，可能会有一些交叉重叠。

配置管理是将应用程序、系统和服务器维持在期望状态的过程。与基础设施即代码的重叠在于，IaC确保你的基础设施处于期望状态，但在此之后，特别是Terraform不会照看你的操作系统设置或应用程序的期望状态，而这就是配置管理工具发挥作用的地方。确保系统和应用程序在随着时间推移发生变化时仍能按预期执行。

配置管理可以防止你进行未经记录的小改动或大改动。

### 场景：为什么你会想要使用配置管理

这个场景或者说为什么你会想要使用配置管理，来见见Dean，他是我们的系统管理员，Dean是一个快乐的露营者，正在处理他环境中的所有系统。

如果系统出现故障，发生火灾，服务器宕机怎么办？Dean完全知道该怎么做，他可以轻松搞定那个火情，但如果多个服务器开始故障，问题对Dean来说就变得非常困难了，特别是当你拥有庞大且不断扩展的环境时，这就是为什么Dean真的需要配置管理工具。配置管理工具可以帮助Dean看起来像个摇滚明星，他只需要配置正确的代码，就能让他快速、有效地大规模推送关于如何设置每台服务器的指令。


### 配置管理工具

有多种配置管理工具可供选择，每种工具都有特定的功能，使其在某些情况下比其他工具更适合。

![](Images/Day63_config1.png)

在这个阶段，我们将快速浏览上图中的选项，然后再决定我们将使用哪一个以及为什么。

- **Chef**
  - Chef确保在任何规模的基础设施自动化中，每个环境都能一致地应用配置。
  - Chef是由OpsCode开发的开源工具，使用Ruby和Erlang编写。
  - Chef最适合拥有异构基础设施并寻求成熟解决方案的组织。
  - Recipes（食谱）和Cookbooks（菜谱）决定了系统的配置代码。
  - 优点 - 有大量可用的recipes集合
  - 优点 - 与Git集成良好，提供了强大的版本控制
  - 缺点 - 学习曲线陡峭，需要相当多的时间投入。
  - 缺点 - 主服务器没有太多控制权。
  - 架构 - 服务器/客户端（Server / Clients）
  - 设置难度 - 中等（Moderate）
  - 语言 - 过程式（Procedural）- 指定如何做一项任务

- **Puppet**
  - Puppet是一种支持自动部署的配置管理工具。
  - Puppet使用Ruby构建，使用DSL编写manifests（清单）。
  - Puppet也适用于异构基础设施，重点是可扩展性。
  - 优点 - 庞大的社区支持。
  - 优点 - 完善的报告机制。
  - 缺点 - 高级任务需要了解Ruby语言。
  - 缺点 - 主服务器没有太多控制权。
  - 架构 - 服务器/客户端（Server / Clients）
  - 设置难度 - 中等（Moderate）
  - 语言 - 声明式（Declarative）- 只指定做什么

- **Ansible**
  - Ansible是一种IT自动化工具，可自动化配置管理、云配置、部署和编排。
  - Ansible playbooks的核心使用YAML编写。（我们真的应该做一个关于YAML的章节，因为我们已经见过好几次了）
  - Ansible在需要快速启动和运行的环境中表现良好。
  - 基于playbooks工作，playbooks为你的服务器提供指令。
  - 优点 - 远程节点不需要代理（agent）。
  - 优点 - YAML易于学习。
  - 缺点 - 性能速度通常低于其他工具（但比Dean手动操作快）
  - 缺点 - YAML不如Ruby强大，但学习曲线较低。
  - 架构 - 仅客户端（Client Only）
  - 设置难度 - 非常简单（Very Easy）
  - 语言 - 过程式（Procedural）- 指定如何做一项任务

- **SaltStack**
  - SaltStack是一种基于CLI的工具，可自动化配置管理和远程执行。
  - SaltStack基于Python，而指令使用YAML或它自己的DSL编写。
  - 非常适合以可扩展性和弹性为优先的环境。
  - 优点 - 上手后易于使用
  - 优点 - 良好的报告机制
  - 缺点 - 设置阶段比较困难
  - 缺点 - 新的Web UI远不如其他工具成熟。
  - 架构 - 服务器/客户端（Server / Clients）
  - 设置难度 - 中等（Moderate）
  - 语言 - 声明式（Declarative）- 只指定做什么

### Ansible vs Terraform

我们将在本节中使用的工具将是Ansible。（易于使用且所需的语言基础更简单。）

我认为在我们进一步了解工具之前，有必要谈谈Ansible和Terraform之间的一些区别。

|               |Ansible                                                        |Terraform                                                          |
| ------------- | ------------------------------------------------------------- | ----------------------------------------------------------------- |
|类型           |Ansible是一种配置管理工具                                      |Terraform是一种编排工具                                            |
|基础设施       |Ansible支持可变基础设施（mutable infrastructure）              |Terraform支持不可变基础设施（immutable infrastructure）              |
|语言           |Ansible遵循过程式语言（procedural language）                   |Terraform遵循声明式语言（declarative language）                      |
|配置           |Ansible提供部分配置能力（VM、Network、Storage）                |Terraform提供广泛的配置能力（VM、Network、Storage）                  |
|打包           |Ansible提供完整的打包和模板支持                                |Terraform提供部分打包和模板支持                                    |
|生命周期管理   |Ansible没有生命周期管理                                        |Terraform严重依赖生命周期和状态管理                                |



## 相关资料

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)


[第64天](day64.md)见。
