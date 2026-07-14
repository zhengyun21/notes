---
title: '#90DaysOfDevOps - 什么是 Docker 及如何安装 - 第43天'
published: false
description: 90DaysOfDevOps - 什么是 Docker 及如何安装
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048739
---
## 什么是 Docker 及如何安装

在上一篇文章中我至少提到了一次 Docker，这是因为 Docker 在使容器变得流行方面确实具有创新性，尽管容器已经存在很长时间了。

我们将在这里使用和解释 Docker，但我们也应该提到 [Open Container Initiative (OCI)](https://www.opencontainers.org/)，这是一个行业标准组织，鼓励创新同时避免供应商锁定的风险。感谢 OCI，我们在选择容器工具链时有了选择，包括 Docker、[CRI-O](https://cri-o.io/)、[Podman](http://podman.io/)、[LXC](https://linuxcontainers.org/) 等。

Docker 是一个用于构建、运行和管理容器的软件框架。术语“docker”可能指的是工具（命令和守护进程）或 Dockerfile 文件格式。

我们将在这里使用 Docker Personal，它是免费的（用于教育和学习）。这包括我们需要涵盖的所有基础知识，以建立对容器和工具的良好基础认识。

也许值得分解一下我们将使用的一些“docker”工具以及它们的用途。docker 这个术语可能指的是 docker 项目整体，这是一个供开发人员和运维人员开发、交付和运行应用程序的平台。它也可能是指在主机上运行的 docker 守护进程进程，它管理镜像和容器，也称为 Docker Engine。

### Docker Engine

Docker Engine 是一种开源容器化技术，用于构建和容器化你的应用程序。Docker Engine 作为一个客户端-服务器应用程序，包含：

- 一个带有长期运行的守护进程 dockerd 的服务器。
- API，指定程序可以用来与 Docker 守护进程通信和指示的接口。
- 一个命令行界面（CLI）客户端 docker。

以上内容来自官方 Docker 文档，具体请参阅 [Docker Engine Overview](https://docs.docker.com/engine/)

### Docker Desktop

我们为 Windows 和 macOS 系统提供了 Docker Desktop。一个易于安装、轻量级的 Docker 开发环境。一个原生操作系统应用程序，利用主机操作系统的虚拟化能力。

如果你想在 Windows 或 macOS 上构建、调试、测试、打包和交付 Docker 化应用程序，这是最佳解决方案。

在 Windows 上，我们还能够利用 WSL2 和 Microsoft Hyper-V。我们将在后续介绍一些 WSL2 的好处。

由于与主机操作系统上的虚拟化管理程序能力集成，Docker 提供了运行带有 Linux 操作系统的容器的能力。

### Docker Compose

Docker Compose 是一个允许你在多个容器上运行更复杂应用程序的工具。好处是能够使用单个文件和命令来启动你的应用程序。

### Docker Hub

一个用于处理 Docker 及其组件的集中式资源。最著名的是作为托管 Docker 镜像的注册表（Registry）。但这里还有很多额外的服务，可以与自动化一起使用，或集成到 GitHub 中，以及安全扫描。

### Dockerfile

Dockerfile 是一个文本文件，包含你通常会手动执行的命令，以便构建一个 Docker 镜像。Docker 可以通过读取我们 Dockerfile 中的指令来自动构建镜像。

## 安装 Docker Desktop

[Docker 文档](https://docs.docker.com/engine/install/)非常棒，如果你刚开始接触，那么你应该看一看并通读一遍。我们将在 Windows 上使用带有 WSL2 的 Docker Desktop。我已经在我正在使用的机器上运行了安装程序。

![](Images/Day43_Containers1.png)

在你继续安装之前，请注意系统要求，[在 Windows 上安装 Docker Desktop](https://docs.docker.com/desktop/windows/install/)，如果你使用的是 macOS，包括基于 M1 的 CPU 架构，你也可以查看 [在 macOS 上安装 Docker Desktop](https://docs.docker.com/desktop/mac/install/)。

我将在另一台 Windows 机器上运行 Docker Desktop 安装过程，并在下面记录该过程。



## 相关资料

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)

[第44天](day44.md)见。
