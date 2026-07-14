---
title: '#90DaysOfDevOps - Docker 镜像与 Docker Desktop 动手实践 - 第44天'
published: false
description: 90DaysOfDevOps - Docker 镜像与 Docker Desktop 动手实践
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048708
---
## Docker 镜像与 Docker Desktop 动手实践

我们现在已经在系统上安装了 Docker Desktop。（如果你运行的是 Linux，你仍然可以选择，但没有 GUI，不过 docker 显然确实可以在 Linux 上工作。）[在 Ubuntu 上安装 Docker Engine](https://docs.docker.com/engine/install/ubuntu/)（其他发行版也可用。）

在这篇文章中，我们将开始在环境中部署一些镜像。回顾一下什么是 Docker 镜像——Docker 镜像是用于在 Docker 容器中执行代码的文件。Docker 镜像作为构建 Docker 容器的一组指令，就像一个模板。Docker 镜像也是使用 Docker 时的起点。

现在是去 [DockerHub](https://hub.docker.com/) 创建你账户的好时机。

![](Images/Day44_Containers1.png)

DockerHub 是一个用于处理 Docker 及其组件的集中式资源。最著名的是作为托管 docker 镜像的注册表（Registry）。但这里还有很多额外的服务，可以与自动化一起使用，或集成到 GitHub 中，以及安全扫描。

登录后如果你向下滚动，你会看到一个容器镜像列表，你可能会看到 mySQL 的数据库镜像、hello-world 等等。把这些当作很好的基础镜像，或者你可能确实只需要一个数据库镜像，而你最好使用官方的，这意味着你不需要创建自己的。

![](Images/Day44_Containers2.png)

我们可以更深入地查看可用镜像，并按类别、操作系统和架构进行搜索。我在下面强调的一点是官方镜像（Official Image），这应该能让你对容器镜像的来源放心。

![](Images/Day44_Containers3.png)

我们还可以搜索特定的镜像，例如 wordpress 可能是一个我们想要的好基础镜像，我们可以在顶部进行搜索，找到所有与 wordpress 相关的容器镜像。请注意，我们还有经过验证的发布者（Verified Publisher）。

- 官方镜像（Official Image）- Docker 官方镜像是一组精选的 Docker 开源和“即插即用”解决方案仓库。

- 验证发布者（Verified Publisher）- 来自验证发布者的高质量 Docker 内容。这些产品直接由商业实体发布和维护。

![](Images/Day44_Containers4.png)

### 探索 Docker Desktop

我们已经在系统上安装了 Docker Desktop，如果打开它，我预计除非你之前已经安装过，否则你会看到与下图类似的内容。如你所见，我们没有正在运行的容器，我们的 docker 引擎正在运行。

![](Images/Day44_Containers5.png)

因为对我来说这不是全新安装，我确实已经下载了一些镜像并在我的系统上可用。你在这里可能什么都看不到。

![](Images/Day44_Containers6.png)

在远程仓库（remote repositories）下，这是你会发现存储在 Docker Hub 中的任何容器镜像的地方。你可以从下面看到，我没有任何镜像。

![](Images/Day44_Containers7.png)

我们也可以在 dockerhub 网站上确认这一点，确认我们在那里没有任何仓库。

![](Images/Day44_Containers8.png)

接下来我们有 Volumes 标签页，如果你有需要持久化的容器，那么这就是我们在本地文件系统或共享文件系统上添加这些卷的地方。

![](Images/Day44_Containers9.png)

在撰写本文时，还有一个 Dev Environments 标签页，这将帮助你与团队协作，而不是在不同的 git 分支之间切换。我们不会介绍这个。

![](Images/Day44_Containers10.png)

回到第一个标签页，你可以看到有一个我们可以运行的命令，它是一个入门容器。让我们在终端中运行 `docker run -d -p 80:80 docker/getting-started`。

![](Images/Day44_Containers11.png)

如果我们再去查看我们的 docker desktop 窗口，我们会看到我们有一个正在运行的容器。

![](Images/Day44_Containers12.png)

你可能注意到我正在使用 WSL2，为了让你能够使用它，你需要确保在设置中启用了这个选项。

![](Images/Day44_Containers13.png)

如果我们现在再去查看我们的 Images 标签页，你现在应该看到一个正在使用的名为 docker/getting-started 的镜像。

![](Images/Day44_Containers14.png)

回到 Containers/Apps 标签页，点击你正在运行的容器。默认情况下你会看到日志，沿着顶部你有一些选项可以选择，在我们的情况下，我非常有信心这将是一个在这个容器中运行的网页，因此我们将选择在浏览器中打开（open in browser）。

![](Images/Day44_Containers15.png)

当我们点击上面的按钮时，果然会打开一个网页，访问你的 localhost 并显示类似于下面的内容。

这个容器还有一些关于什么是容器和镜像的更多细节。

![](Images/Day44_Containers16.png)

我们现在已经运行了我们的第一个容器。到目前为止还没有什么可怕的。如果我们想从 DockerHub 拉取一个容器镜像呢？也许有一个 `hello world` docker 容器我们可以使用。

我提前停止了 getting started 容器，并不是说它占用了大量资源，而是为了在我们继续介绍更多步骤时保持整洁。

回到我们的终端，让我们继续运行 `docker run hello-world`，看看会发生什么。

你可以看到我们本地没有这个镜像，所以我们把它拉取下来，然后我们得到了一条写入容器镜像中的消息，其中包含一些关于它如何启动和运行以及一些参考链接的信息。

![](Images/Day44_Containers17.png)

然而，如果我们现在查看 Docker Desktop，我们没有正在运行的容器，但我们确实有一个已退出（exited）的容器，它使用了 hello-world 消息，这意味着它启动了，传递了消息，然后终止了。

![](Images/Day44_Containers18.png)

最后一次，让我们去查看 images 标签页，看看我们在本地系统上有一个新的 hello-world 镜像，这意味着如果我们在终端中再次运行 `docker run hello-world` 命令，我们将不必拉取任何东西，除非版本发生变化。

![](Images/Day44_Containers19.png)

在 hello-world 容器的消息中，它提出了一个挑战，要运行一些更有野心的东西。

接受挑战！

![](Images/Day44_Containers20.png)

在终端中运行 `docker run -it ubuntu bash`，我们将运行一个容器化的 Ubuntu 版本——嗯，不是操作系统的完整副本。你可以在 [DockerHub](https://hub.docker.com/_/ubuntu) 上找到有关此特定镜像的更多信息。

你可以在下面看到，当我们运行命令时，我们现在有了一个交互式提示符（`-it`），并且我们已经进入了容器的 bash shell。

![](Images/Day44_Containers21.png)

我们有一个 bash shell，但我们没有更多东西了，这就是为什么这个容器镜像不到30MB。

![](Images/Day44_Containers22.png)

但我们仍然可以使用这个镜像，我们仍然可以使用我们的 apt 包管理器安装软件，我们也可以更新我们的容器镜像并进行升级。

![](Images/Day44_Containers23.png)

或者也许我们想在我们的容器中安装一些软件，我在这里选择了一个非常糟糕的例子，因为 pinta 是一个图像编辑器，它超过200MB，但希望你能明白我的意思。这将大大增加我们容器的大小，但仍然我们会以 MB 为单位，而不会进入 GB。

![](Images/Day44_Containers24.png)

我希望这能让你对 Docker Desktop 和容器的世界有一个大致的了解，当你用简单的用例来分解它时，它并没有那么可怕，我们需要涵盖一些网络、安全以及其他选项，而不仅仅是下载容器镜像并像这样使用它们。到本节结束时，我们希望已经制作了一些东西并上传到我们的 DockerHub 仓库，并能够部署它。

## 相关资料

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)

[第45天](day45.md)见。
