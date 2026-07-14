---
title: '#90DaysOfDevOps - Docker Compose - 第46天'
published: false
description: 90DaysOfDevOps - Docker Compose
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048740
---
## Docker Compose

运行一个容器的能力可能很好，如果你有一个自包含的镜像，里面有你单一用例所需的一切，但当涉及到在不同的容器镜像之间构建多个应用程序时，事情就变得有趣了。例如，如果我有一个网站前端，但需要一个后端数据库，我可以把所有东西放在一个容器里，但更好更高效的做法是为数据库单独设置一个容器。

这就是 Docker Compose 的用武之地，它是一个允许你在多个容器上运行更复杂应用程序的工具。好处是能够使用单个文件和命令来启动你的应用程序。我在这篇文章中将要演示的示例来自 [Docker QuickStart sample apps (Quickstart: Compose and WordPress)](https://docs.docker.com/samples/wordpress/)。

在第一个示例中，我们将：

- 使用 Docker Compose 启动 WordPress 和一个单独的 MySQL 实例。
- 使用一个名为 `docker-compose.yml` 的 YAML 文件
- 构建项目
- 通过浏览器配置 WordPress
- 关闭并清理

### 安装 Docker Compose

如前所述，Docker Compose 是一个工具，如果你在 macOS 或 Windows 上，那么 compose 已包含在你的 Docker Desktop 安装中。然而，你可能想在 Windows 服务器主机或 Linux 服务器上运行你的容器，在这种情况下，你可以使用这些说明进行安装 [Install Docker Compose](https://docs.docker.com/compose/install/)

要确认我们的系统上已安装 `docker-compose`，我们可以打开一个终端并简单地输入上述命令。

![](Images/Day46_Containers1.png)

### Docker-Compose.yml (YAML)

接下来要谈的是 docker-compose.yml，你可以在仓库的 container 文件夹中找到它。但更重要的是，我们需要一般性地讨论一下 YAML。

YAML 几乎可以有它自己的一节，因为你会在很多不同的地方找到它。但大多数情况下

"YAML 是一种对人类友好的数据序列化语言，适用于所有编程语言。"

它通常用于配置文件，以及在某些数据正在被存储或传输的应用程序中。你一定遇到过 XML 文件，它们往往提供相同的配置文件功能。YAML 提供了一种最小化的语法，但针对的是相同的用例。

YAML Ain't Markup Language (YAML) 是一种序列化语言，在过去几年中稳步增加了 popularity。对象序列化能力使其成为 JSON 等语言的可行替代品。

YAML 这个缩写原本是 Yet Another Markup Language 的简写。但维护者将其重命名为 YAML Ain't Markup Language，以更加强调其面向数据的特性。

好了，回到 docker-compose.yml 文件。这是一个配置文件，说明当我们想在单个系统上部署多个容器时我们想做什么。

直接从上面链接的教程中，你可以看到该文件的内容如下所示：

```
version: "3.9"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```

我们声明一个版本，然后这个 docker-compose.yml 文件的很大一部分由我们的服务组成，我们有一个 db 服务和一个 wordpress 服务。你可以看到每个服务都定义了一个镜像，并关联了一个版本标签。与我们的第一次演示不同，现在我们正在将状态引入我们的配置，但现在我们要创建卷（volumes），以便我们可以将数据库存储在那里。

然后我们有一些环境变量，例如密码和用户名。显然这些文件可以变得非常复杂，但 YAML 配置文件简化了这些整体外观。

### 构建项目

接下来我们可以回到终端，使用 docker-compose 工具的一些命令。导航到你的目录，你的 docker-compose.yml 文件所在的位置。

从终端我们可以简单地运行 `docker-compose up -d`，这将开始拉取这些镜像并启动你的多容器应用程序。

命令中的 `-d` 表示分离模式（detached mode），这意味着运行命令将在后台进行。

![](Images/Day46_Containers2.png)

如果我们现在运行 `docker ps` 命令，你可以看到我们有2个容器正在运行，一个是 wordpress，另一个是 MySQL。

![](Images/Day46_Containers3.png)

接下来我们可以通过打开浏览器并访问 `http://localhost:8000` 来验证 WordPress 是否已启动并运行，你应该会看到 wordpress 设置页面。

![](Images/Day46_Containers4.png)

我们可以运行 WordPress 的设置，然后我们可以开始在下面的控制台中按我们认为合适的方式构建我们的网站。

![](Images/Day46_Containers5.png)

如果我们然后打开一个新标签页并导航到之前的相同地址 `http://localhost:8000`，我们现在会看到一个简单的默认主题，网站标题为 "90DaysOfDevOps"，然后是一篇示例文章。

![](Images/Day46_Containers6.png)

在做出任何更改之前，打开 Docker Desktop 并导航到 volumes 标签页，在这里你会看到与我们的容器相关联的两个卷，一个用于 wordpress，一个用于 db。

![](Images/Day46_Containers7.png)

我当前的 wordpress 主题是 "Twenty Twenty-Two"，我想把它改成 "Twenty Twenty"。回到仪表板，我们可以进行这些更改。

![](Images/Day46_Containers8.png)

我还要在我的网站上添加一篇新文章，在下面你会看到我们新网站的最新版本。

![](Images/Day46_Containers9.png)

### 清理或不清理

如果我们现在使用 `docker-compose down` 命令，这将关闭我们的容器。但会保留我们的卷。

![](Images/Day46_Containers10.png)

我们可以在 Docker Desktop 中确认我们的卷仍然在那里。

![](Images/Day46_Containers11.png)

如果我们然后想重新启动东西，我们可以在同一目录下发出 `docker up -d` 命令，我们的应用程序就会恢复运行。

![](Images/Day46_Containers12.png)

然后我们导航到浏览器中的相同地址 `http://localhost:8000`，注意我们的新文章和主题更改都还在。

![](Images/Day46_Containers13.png)

如果我们想摆脱容器和那些卷，那么发出 `docker-compose down --volumes` 也会销毁卷。

![](Images/Day46_Containers14.png)

现在当我们再次使用 `docker-compose up -d` 时，我们将重新开始，然而镜像仍然会在我们的本地系统上，所以你不需要从 DockerHub 仓库重新拉取它们。

我知道当我开始深入研究 docker-compose 及其功能时，我接着对它与容器编排（Container Orchestration）工具（如 Kubernetes）的关系感到困惑，好吧，我们在这个简短演示中所做的一切都集中在一个主机上，我们在本地桌面机器上运行 wordpress 和 db。我们没有多台虚拟机或多台物理机，我们也没有能力轻松地根据应用程序的需求进行扩展和缩减。

我们的下一节将涵盖 Kubernetes，但我们还有几天关于容器的一般内容要先介绍。

这里也是一个很棒的资源，用于 docker compose 应用程序与多个集成的示例。[Awesome-Compose](https://github.com/docker/awesome-compose)

在上面那个仓库中有一个很棒的示例，将在单节点上部署 Elasticsearch、Logstash 和 Kibana (ELK)。

我已将文件上传到 [Containers folder](2022/Days/Containers/elasticsearch-logstash-kibana/) 当你本地有这个文件夹时，导航到那里，你可以简单地使用 `docker-compose up -d`

![](Images/Day46_Containers15.png)

然后我们可以用 `docker ps` 检查这些正在运行的容器

![](Images/Day46_Containers16.png)

现在我们可以为每个容器打开浏览器：

![](Images/Day46_Containers17.png)

要删除所有内容，我们可以使用 `docker-compose down` 命令。

## 相关资料

- [TechWorld with Nana - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=3c-iBn73dDE)
- [Programming with Mosh - Docker Tutorial for Beginners](https://www.youtube.com/watch?v=pTFZFxd4hOI)
- [Docker Tutorial for Beginners - What is Docker? Introduction to Containers](https://www.youtube.com/watch?v=17Bl31rlnRM&list=WL&index=128&t=61s)
- [WSL 2 with Docker getting started](https://www.youtube.com/watch?v=5RQbdMn04Oc)
- [Blog on gettng started building a docker image](https://stackify.com/docker-build-a-beginners-guide-to-building-docker-images/)
- [Docker documentation for building an image](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
- [YAML Tutorial: Everything You Need to Get Started in Minute](https://www.cloudbees.com/blog/yaml-tutorial-everything-you-need-get-started)

[第47天](day47.md)见。
