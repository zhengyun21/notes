---
title: '#90DaysOfDevOps - Docker 镜像的解剖结构 - 第45天'
published: false
description: 90DaysOfDevOps - Docker 镜像的解剖结构
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048777
---
## Docker 镜像的解剖结构

在上一节课中，我们介绍了如何使用 Docker Desktop 结合 DockerHub 来部署和运行一些经过验证的镜像的基础知识。回顾一下什么是镜像，如果我一直提到你就不会忘记。

Docker 镜像是一个只读模板，包含一组用于在 Docker 平台上创建容器的指令。它提供了一种便捷的方式来打包应用程序和预配置的服务器环境，你可以用于自己的私人用途或公开与其他 Docker 用户共享。Docker 镜像也是任何第一次使用 Docker 的人的起点。

如果我们想创建自己的 Docker 镜像怎么办？为了做到这一点，我们将创建一个 Dockerfile。你看到了我们如何获取那个 Ubuntu 容器镜像并添加我们的软件，然后我们就会有我们想要的软件的容器镜像，一切都很好，但是如果该容器被关闭或丢弃，那么所有这些软件更新和安装都会消失，没有一个可重复的版本来说明我们做了什么。所以这很适合展示能力，但它无助于在多个环境中传输镜像，每次容器运行时都安装相同的软件集。

### 什么是 Dockerfile

Dockerfile 是一个文本文件，包含你通常会手动执行的命令，以便构建一个 Docker 镜像。Docker 可以通过读取我们 Dockerfile 中的指令来自动构建镜像。

构成 Docker 镜像的每个文件都被称为一个层（layer）。这些层形成了一系列镜像，分阶段相互构建。每一层都直接依赖于它下面的一层。层的顺序对于 Docker 镜像生命周期管理的效率至关重要。

我们应该将更改最频繁的层组织在堆栈的尽可能高的位置，这是因为当你对镜像中的某一层进行更改时，Docker 不仅会重建该特定层，还会重建所有从它构建的层。因此，对顶层进行更改涉及重建整个镜像的工作量最少。

每次 Docker 从镜像启动容器时（就像我们昨天运行的一样），它都会添加一个可写层，称为容器层（container layer）。这存储了容器在整个运行期间的所有更改。这一层是实时运行中的容器与源镜像本身之间的唯一区别。任意数量的相同容器都可以共享对同一底层镜像的访问，同时保持它们自己的独立状态。

回到我们昨天使用的 Ubuntu 镜像示例。我们可以多次运行相同的命令，在第一个容器中我们可以去安装 pinta，在第二个中我们可以安装 figlet，两个不同的应用程序，不同的用途，不同的大小等等。我们部署的每个容器都共享相同的镜像，但不共享相同的状态，然后当我们删除容器时，该状态也就消失了。

![](Images/Day45_Containers1.png)

按照上面的 Ubuntu 镜像示例，以及 DockerHub 和其他第三方仓库上许多其他现成的容器镜像。这些镜像通常被称为父镜像（parent image）。它是所有其他层构建的基础，为我们的容器环境提供基本的构建块。

除了单个层文件集之外，Docker 镜像还包含一个称为清单（manifest）的附加文件。这本质上是 JSON 格式的镜像描述，包含诸如镜像标签、数字签名以及如何为不同类型的主机平台配置容器的详细信息。

![](Images/Day45_Containers2.png)

### 如何创建 Docker 镜像

有两种方法可以创建 Docker 镜像。我们可以用我们昨天开始的流程即时完成，我们选择我们的基础镜像，启动该容器，我们安装所有我们希望容器上拥有的软件和依赖项。

然后我们可以使用 `docker commit container name`，然后我们在 docker images 和 Docker Desktop 的 images 标签页下有该镜像的本地副本。

超级简单，除非你想了解这个过程，否则我不推荐这种方法，用这种方式管理生命周期将非常困难，而且需要大量的手动配置/重新配置。但它是构建 Docker 镜像最快最简单的方法。非常适合测试、故障排除、验证依赖项等。

我们打算构建镜像的方式是通过 Dockerfile。这为我们提供了一种干净、紧凑且可重复的方式来创建我们的镜像。更容易的生命周期管理，并且易于集成到持续集成（Continuous Integration）和持续交付（Continuous Delivery）流程中。但正如你可能已经猜到的，它比第一种提到的方法稍微困难一些。

使用 Dockerfile 方法更符合现实世界、企业级的容器部署。

Dockerfile 是一个三步流程，你创建 Dockerfile 并添加组装镜像所需的命令。

下表显示了一些我们将要使用的或你最有可能使用的 Dockerfile 指令。

| 指令       | 用途                                                                                                                                       |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| FROM       | 指定父镜像。                                                                                                                               |
| WORKDIR    | 设置 Dockerfile 中后续命令的工作目录。                                                                                                     |
| RUN        | 安装容器所需的任何应用程序和包。                                                                                                           |
| COPY       | 从特定位置复制文件或目录。                                                                                                                 |
| ADD        | 与 COPY 类似，但还能够处理远程 URL 和解压压缩文件。                                                                                        |
| ENTRYPOINT | 容器启动时始终执行的命令。如果未指定，默认值为 /bin/sh -c                                                                                  |
| CMD        | 传递给 ENTRYPOINT 的参数。如果未设置 ENTRYPOINT（默认值为 /bin/sh -c），CMD 将是容器执行的命令。                                          |
| EXPOSE     | 定义通过哪个端口访问你的容器应用程序。                                                                                                     |
| LABEL      | 向镜像添加元数据。                                                                                                                         |


现在我们有了如何构建第一个 Dockerfile 的详细信息，我们可以创建一个工作目录并创建我们的 Dockerfile。我在这个仓库中创建了一个工作目录，你可以看到我为了演示所准备的文件和文件夹。[Containers](Days/Containers)

在这个目录中，我将创建一个 .dockerignore 文件，类似于我们在上一节中使用的 .gitignore。该文件将列出在 Docker 构建过程中否则会创建的、你希望从最终构建中排除的任何文件。

记住，关于容器的一切都是关于紧凑，尽可能快，没有冗余。

我想创建一个非常简单的 Dockerfile，布局如下，也可以在上方链接的文件夹中找到。

```
# Use the official Ubuntu 18.04 as base
FROM ubuntu:18.04
# Install nginx and curl
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y nginx curl
RUN rm -rf /var/lib/apt/lists/*
```

在终端中导航到此目录，然后运行 `docker build -t 90daysofdevops:0.1 .`，我们使用 `-t` 然后设置镜像名称和标签。

![](Images/Day45_Containers3.png)

现在我们创建了镜像，我们可以使用 Docker Desktop 运行我们的镜像，或者我们可以使用 docker 命令行。我使用了 Docker Desktop，我启动了一个容器，你可以看到我们在容器的 cli 中有 `curl` 可用。

![](Images/Day45_Containers4.png)

虽然在 Docker Desktop 中还可以利用 UI 对这个新镜像执行更多任务。

![](Images/Day45_Containers5.png)

我们可以检查我们的镜像，这样做你会看到非常多的 Dockerfile 和我们想在容器中运行的代码行。

![](Images/Day45_Containers6.png)

我们有一个拉取（pull）选项，现在这对我们会失败，因为这个镜像没有托管在任何地方，所以我们会收到错误。然而我们确实有一个推送到 hub（Push to hub），这将使我们能够将镜像推送到 DockerHub。

如果你使用的是我们之前运行的相同的 `docker build`，那么这也不起作用，你需要将构建命令改为 `docker build -t {{username}}/{{imagename}}:{{version}}`

![](Images/Day45_Containers7.png)

然后如果我们去查看我们的 DockerHub 仓库，你可以看到我们刚刚推送了一个新镜像。现在在 Docker Desktop 中我们将能够使用那个拉取（pull）标签页。

![](Images/Day45_Containers8.png)


## 相关资料

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on gettng started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

[第46天](day46.md)见。
