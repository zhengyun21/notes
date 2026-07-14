---
title: '#90DaysOfDevOps - Docker 的替代方案 - 第48天'
published: false
description: 90DaysOfDevOps - Docker 的替代方案
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048807
---
## Docker 的替代方案

我在本节开头就说过，我们将使用 Docker，这仅仅是因为资源丰富且社区非常庞大，而且这也是让容器真正流行起来的地方。我鼓励你去了解 Docker 的历史及其起源，我发现这非常有用。

但正如我所暗示的，Docker 还有其他替代方案。如果我们想想 Docker 是什么以及我们已经涵盖的内容。它是一个用于开发、测试、部署和管理应用程序的平台。

我想重点介绍几个你可能会在未来看到的 Docker 替代方案。

### Podman

什么是 Podman？Podman 是一个无守护进程（daemonless）的容器引擎，用于在 Linux 系统上开发、管理和运行 OCI 容器。容器可以以 root 或 rootless 模式运行。

我将从 Windows 的角度来介绍这个，但要知道，与 Docker 一样，它在 Linux 上不需要虚拟化，因为它会使用底层操作系统，而在 Windows 世界中这是做不到的。

Podman 可以在 WSL2 下运行，尽管体验不如 Docker Desktop 那样流畅。还有一个 Windows 远程客户端，你可以连接到一个 Linux 虚拟机，你的容器将在那里运行。

我的 WSL2 上的 Ubuntu 是 20.04 版本。按照以下步骤，你可以在你的 WSL 实例上安装 Podman。

```
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/xUbuntu_20.04/ /" |
sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
```

添加 GPG 密钥

```
curl -L "https://download.opensuse.org/repositories/devel:/kubic:\
/libcontainers:/stable/xUbuntu_20.04/Release.key" | sudo apt-key add -
```

使用 `sudo apt-get update && sudo apt-get upgrade` 命令运行系统更新和升级。最后我们可以使用 `sudo apt install podman` 安装 podman。

现在我们可以使用很多与 docker 相同的命令，不过请注意，我们没有那个漂亮的 Docker Desktop UI。你可以在下面看到，我使用了 `podman images`，安装后什么都没有，然后我使用了 `podman pull ubuntu` 来拉取 ubuntu 容器镜像。

![](Images/Day48_Containers1.png)

然后我们可以使用 `podman run -dit ubuntu` 运行我们的 Ubuntu 镜像，并使用 `podman ps` 查看正在运行的镜像。

![](Images/Day48_Containers2.png)

要进入该容器，我们可以运行 `podman attach dazzling_darwin`（你的容器名称可能会不同）。

![](Images/Day48_Containers3.png)

如果你要从 docker 迁移到 podman，通常还会更改配置文件以添加 `alias docker=podman`，这样你运行的任何 docker 命令实际上都会使用 podman。

### LXC

LXC 是一个容器化引擎，使用户能够再次创建多个隔离的 Linux 容器环境。与 Docker 不同，LXC 充当虚拟机管理程序（hypervisor），用于创建多个具有独立系统文件、网络功能的 Linux 机器。它在 Docker 之前就已经存在，然后由于 Docker 的不足而短暂复出。

LXC 和 docker 一样轻量级，并且易于部署。

### Containerd

一个独立的容器运行时（container runtime）。Containerd 带来了简单性、稳健性以及可移植性。Containerd 以前是作为 Docker 容器服务的一部分运行的工具，直到 Docker 决定将其组件升级为独立组件。

Cloud Native Computing Foundation（CNCF）的一个项目，使其与 Kubernetes、Prometheus 和 CoreDNS 等流行的容器工具处于同一级别。

### 其他 Docker 工具

我们还可以提到 Rancher、VirtualBox 等工具和选项，但我们可以下次再更详细地介绍它们。

[**Gradle**](https://gradle.org/)

- 构建扫描（Build scans）允许团队协同调试他们的脚本并跟踪所有构建的历史。
- 执行选项使团队能够持续构建，这样每当有更改输入时，任务就会自动执行。
- 自定义仓库布局使团队能够将任何文件目录结构视为构件仓库。

[**Packer**](https://packer.io/)

- 能够并行创建多个机器镜像，以节省开发人员时间并提高效率。
- 团队可以使用 Packer 的调试器轻松调试构建，该调试器检查故障并允许团队在重新启动构建之前尝试解决方案。
- 通过插件支持许多平台，以便团队可以自定义他们的构建。

[**Logspout**](https://github.com/gliderlabs/logspout)

- 日志工具 — 该工具的可定制性允许团队将相同的日志发送到多个目的地。
- 团队可以轻松管理他们的文件，因为该工具只需要访问 Docker socket。
- 完全开源且易于部署。

[**Logstash**](https://www.elastic.co/products/logstash)

- 使用 Logstash 的可插拔框架自定义你的管道。
- 轻松解析和转换你的数据以进行分析并交付业务价值。
- Logstash 的多种输出让你可以将数据路由到你想要的地方。

[**Portainer**](https://www.portainer.io/)

- 利用预制模板或创建你自己的模板来部署应用程序。
- 创建团队并为团队成员分配角色和权限。
- 使用该工具的仪表板了解每个环境中正在运行的内容。

## 资源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on gettng started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)
- [Podman | Daemonless Docker | Getting Started with Podman](https://www.youtube.com/watch?v=Za2BqzeZjBk)
- [LXC - Guide to building a LXC Lab](https://www.youtube.com/watch?v=cqOtksmsxfg)
-


[第49天](day49.md)见。
