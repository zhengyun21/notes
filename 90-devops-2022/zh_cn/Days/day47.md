---
title: '#90DaysOfDevOps - Docker 网络与安全 - 第47天'
published: false
description: 90DaysOfDevOps - Docker 网络与安全
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049078
---
## Docker 网络与安全

在本次容器课程中，到目前为止我们让事情运转起来了，但我们并没有真正深入了解其背后的工作原理，无论是从网络角度，还是我们尚未涉及的安全方面，这就是本次课程的计划。

### Docker 网络基础

打开终端，输入命令 `docker network`，这是配置和管理容器网络的主要命令。

从下面可以看到这个命令的使用方式以及所有可用的子命令。我们可以创建新网络、列出已有网络、检查和删除网络。

![](Images/Day47_Containers1.png)

让我们看一下自安装以来已有的网络，使用 `docker network list` 命令查看 Docker 开箱即用的网络配置。

每个网络都有一个唯一的 ID 和 NAME。每个网络也与单个驱动程序相关联。请注意，"bridge" 网络和 "host" 网络的名称与它们各自的驱动程序相同。

![](Images/Day47_Containers2.png)

接下来，我们可以使用 `docker network inspect` 命令更深入地查看我们的网络。

通过运行 `docker network inspect bridge`，我可以获取该特定网络名称的所有配置详情。这包括名称、ID、驱动程序、已连接的容器，以及如你所见还有很多其他信息。

![](Images/Day47_Containers3.png)

### Docker：桥接网络（Bridge Networking）

如你在上面所见，标准的 Docker Desktop 安装会为我们提供一个预构建的网络，称为 `bridge`。如果你回看 `docker network list` 命令，你会看到名为 bridge 的网络与 `bridge` 驱动程序相关联。仅仅因为它们有相同的名称，并不意味着它们是同一样东西。有关联但不是同一样东西。

上面的输出还显示桥接网络的作用域是本地的。这意味着该网络只存在于这个 Docker 主机上。所有使用 bridge 驱动程序的网络都是如此 — bridge 驱动程序提供单主机网络功能。

所有使用 bridge 驱动程序创建的网络都基于 Linux bridge（又称虚拟交换机）。

### 连接容器

默认情况下，新容器会被分配到 bridge 网络，这意味着除非你指定一个网络，否则所有容器都会连接到 bridge 网络。

让我们使用命令 `docker run -dt ubuntu sleep infinity` 创建一个新容器。

上面的 sleep 命令只是让容器在后台持续运行，这样我们就可以对其进行操作。

![](Images/Day47_Containers4.png)

如果我们然后使用 `docker network inspect bridge` 检查我们的 bridge 网络，你会看到有一个容器与我们刚刚部署的相匹配，因为我们没有指定网络。

![](Images/Day47_Containers5.png)

我们还可以使用 `docker exec -it 3a99af449ca2 bash` 进入容器内部，你需要使用 `docker ps` 来获取你的容器 ID。

从这里开始，我们的镜像没有安装 ping 工具，所以我们需要运行以下命令：`apt-get update && apt-get install -y iputils-ping`，然后 ping 一个外部可访问的地址：`ping -c5 www.90daysofdevops.com`。

![](Images/Day47_Containers6.png)

要清理这个环境，我们可以运行 `docker stop 3a99af449ca2`，同样使用 `docker ps` 找到你的容器 ID，这将移除我们的容器。

### 为外部连接配置 NAT

在这一步中，我们将启动一个新的 NGINX 容器，并将 Docker 主机上的 8080 端口映射到容器内部的 80 端口。这意味着访问 Docker 主机 8080 端口的流量将被转发到容器内部的 80 端口。

通过运行 `docker run --name web1 -d -p 8080:80 nginx` 启动一个基于官方 NGINX 镜像的新容器。

![](Images/Day47_Containers7.png)

通过运行 `docker ps` 查看容器状态和端口映射。

![](Images/Day47_Containers8.png)

第一行显示了运行 NGINX 的新 web1 容器。注意容器正在运行的命令以及端口映射 — `0.0.0.0:8080->80/tcp` 将所有主机接口上的 8080 端口映射到 web1 容器内部的 80 端口。这个端口映射实际上使容器的 Web 服务可以从外部访问（通过 Docker 主机的 IP 地址和 8080 端口）。

现在我们需要获取实际主机的 IP 地址，我们可以通过进入 WSL 终端并使用 `ip addr` 命令来完成。

![](Images/Day47_Containers9.png)

然后我们可以获取这个 IP 并打开浏览器，访问 `http://172.25.218.154:8080/`（你的 IP 可能不同）。这确认了 NGINX 是可访问的。

![](Images/Day47_Containers10.png)

我从 2017 年 DockerCon 的这个网站获取了这些说明，但直到今天它们仍然相关。不过，其余的演练涉及 Docker Swarm，而我不打算在这里深入探讨。[Docker Networking - DockerCon 2017](https://github.com/docker/labs/tree/master/dockercon-us-2017/docker-networking)

### 保护你的容器

与完整的服务器配置相比，容器为你的工作负载提供了一个安全的环境。它们能够将你的应用程序分解成更小、松耦合的组件，每个组件彼此隔离，这有助于整体减少攻击面。

但它们也并非对寻求利用系统的黑客免疫。我们仍然需要了解该技术的安全陷阱并保持最佳实践。

### 远离 root 权限

我们部署的所有容器都使用了 root 权限来运行容器内的进程。这意味着它们对你的容器和主机环境具有完全的管理访问权限。就演练目的而言，我们知道这些系统不会长时间运行。但你已经看到了启动和运行是多么容易。

我们可以在流程中增加几个步骤，使非 root 用户成为我们的首选最佳实践。在创建我们的 Dockerfile 时，我们可以创建用户账户。你也可以在仓库的 containers 文件夹中找到这个示例。

```
# 使用官方 Ubuntu 18.04 作为基础镜像
FROM ubuntu:18.04
RUN apt-get update && apt-get upgrade -y
RUN groupadd -g 1000 basicuser && useradd -r -u 1000 -g basicuser basicuser
USER basicuser
```

我们还可以使用 `docker run --user 1009 ubuntu`，Docker run 命令会覆盖 Dockerfile 中指定的任何用户。因此，在以下示例中，你的容器将始终以最小权限运行 — 前提是用户标识符 1009 也具有最低权限级别。

然而，这种方法并不能解决镜像本身存在的潜在安全缺陷。因此，最好在 Dockerfile 中指定一个非 root 用户，这样你的容器就能始终安全地运行。

### 私有镜像仓库（Private Registry）

我们大量使用的另一个领域是 DockerHub 上的公共镜像仓库，而由你的组织建立的私有容器镜像仓库意味着你可以自行托管，或者也有托管服务可用，但总而言之，这让你可以完全控制你和你的团队可用的镜像。

DockerHub 很适合为你提供基线，但它只提供基本服务，你必须对镜像发布者给予很大的信任。

### 精简与干净

我在整篇文章中都提到过，虽然这与安全没有直接关系。但容器的大小也会影响安全性，就攻击面而言，如果你的应用程序中有你不使用的资源，那么你就不需要将它们放在容器中。

这也是我对拉取 `latest` 镜像的主要担忧，因为这也会给你的镜像带来很多臃肿。DockerHub 确实显示了仓库中每个镜像的压缩大小。

检查 `docker image` 是一个查看镜像大小的绝佳命令。

![](Images/Day47_Containers11.png)

## 资源

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on gettng started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)

[第48天](day48.md)见。
