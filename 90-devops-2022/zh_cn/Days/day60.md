---
title: '#90DaysOfDevOps - Docker容器、Provisioners和模块 - 第60天'
published: false
description: '90DaysOfDevOps - Docker容器、Provisioners和模块'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049052
---
## Docker容器、Provisioners和模块

在[第59天](day59.md)中，我们使用Terraform在本地免费的VirtualBox环境中配置了一台虚拟机。在本节中，我们将部署一个带有一些配置的Docker容器到本地Docker环境中。

### Docker演示

首先，我们将使用下面的代码块，其目标是将一个简单的Web应用部署到Docker中，并将其发布到我们的网络中。我们将使用nginx，并使其在我们的笔记本上通过localhost和8000端口对外可用。我们使用的是来自社区的Docker提供程序（provider），你也可以在我们的配置中看到所使用的Docker镜像。

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "2.16.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
```

第一个任务是使用`terraform init`命令将提供程序下载到我们的本地机器上。

![](Images/Day60_IAC1.png)

然后我们运行`terraform apply`，接着运行`docker ps`，你可以看到我们有一个正在运行的容器。

![](Images/Day60_IAC2.png)

如果我们打开浏览器，可以访问 http://localhost:8000/ ，你会看到我们可以访问到我们的NGINX容器。

![](Images/Day60_IAC3.png)

你可以在[Docker Provider](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)上找到更多信息。

上面是一个非常简单的演示，展示了Terraform加上Docker可以做什么，以及我们如何将其纳入Terraform状态管理。我们在容器部分介绍过docker compose，这与基础设施即代码以及Kubernetes之间在某种程度上存在一些交叉。

为了展示这一点以及Terraform如何处理稍微复杂一些的场景，我们将把之前用docker compose创建的wordpress和mysql的docker compose文件转换为Terraform。你可以在[docker-wordpress.tf](2022/Days/IaC/Docker-Wordpress/docker-wordpress.tf)找到它。

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "2.16.0"
    }
  }
}

provider "docker" {}

variable wordpress_port {
  default = "8080"
}

resource "docker_volume" "db_data" {
  name = "db_data"
}

resource "docker_network" "wordpress_net" {
  name = "wordpress_net"
}

resource "docker_container" "db" {
  name  = "db"
  image = "mysql:5.7"
  restart = "always"
  network_mode = "wordpress_net"
  env = [
     "MYSQL_ROOT_PASSWORD=wordpress",
     "MYSQL_PASSWORD=wordpress",
     "MYSQL_USER=wordpress",
     "MYSQL_DATABASE=wordpress"
  ]
  mounts {
    type = "volume"
    target = "/var/lib/mysql"
    source = "db_data"
    }
}

resource "docker_container" "wordpress" {
  name  = "wordpress"
  image = "wordpress:latest"
  restart = "always"
  network_mode = "wordpress_net"
  env = [
    "WORDPRESS_DB_HOST=db:3306",
    "WORDPRESS_DB_USER=wordpress",
    "WORDPRESS_DB_NAME=wordpress",
    "WORDPRESS_DB_PASSWORD=wordpress"
  ]
  ports {
    internal = "80"
    external = "${var.wordpress_port}"
  }
}
```

我们再次将其放在一个新文件夹中，然后运行`terraform init`命令来拉取所需的提供程序。

![](Images/Day60_IAC4.png)

然后我们运行`terraform apply`命令，再查看docker ps的输出，我们应该能看到新创建的容器。

![](Images/Day60_IAC5.png)

然后我们也可以访问我们的WordPress前端。就像我们在容器部分使用docker-compose经历这个过程时一样，我们现在可以运行安装程序，我们的WordPress文章将存储在我们的MySQL数据库中。

![](Images/Day60_IAC6.png)

显然，既然我们已经详细介绍过容器和Kubernetes，我们知道这对于测试来说是可以的，但如果你真的要运行一个网站，你不会只用容器来完成，你会考虑使用Kubernetes来实现。接下来我们将看看如何使用Terraform与Kubernetes配合。


### Provisioners（配置器）

如果某些东西无法实现声明式管理，Provisioners就提供了一种将其传递给部署的方式。

如果你没有其他选择，并且需要向代码中添加这种复杂性，那么你可以通过运行类似于下面这样的代码块来实现。

```
resource "docker_container" "db" {
  # ...

  provisioner "local-exec" {
    command = "echo The server's IP address is ${self.private_ip}"
  }
}

```

remote-exec provisioner在远程资源创建后调用脚本。这可以用于特定于操作系统的事情，或者用于封装配置管理工具。尽管注意到其中一些有自己的provisioners。

[更多关于provisioners的详细信息](https://www.terraform.io/language/resources/provisioners/syntax)

- file
- local-exec
- remote-exec
- vendor
    - ansible
    - chef
    - puppet

### 模块（Modules）

模块是多个一起使用的资源的容器。一个模块由同一目录中的一组.tf文件组成。

模块是分离基础设施资源的好方法，同时也可以引入已经创建的第三方模块，这样你就不必重复造轮子。

例如，如果我们想在同一个项目中构建一些虚拟机、VPC、安全组以及Kubernetes集群，我们可能会希望将资源拆分成模块，以更好地定义资源以及它们的分组方式。

模块的另一个好处是，你可以将这些模块用于其他项目，或者公开分享以帮助社区。

我们正在将基础设施分解成组件，这些组件在这里被称为模块。

## 相关资料
我在下面列出了很多资料，我认为这个话题已经被反复讨论过很多次了。如果你有额外的资源，请务必提交PR，我很乐意审查并将它们添加到列表中。

- [What is Infrastructure as Code? Difference of Infrastructure as Code Tools](https://www.youtube.com/watch?v=POPP2WTJ8es)
- [Terraform Tutorial | Terraform Course Overview 2021](https://www.youtube.com/watch?v=m3cKkYXl-8o)
- [Terraform explained in 15 mins | Terraform Tutorial for Beginners](https://www.youtube.com/watch?v=l5k1ai_GBDE)
- [Terraform Course - From BEGINNER to PRO!](https://www.youtube.com/watch?v=7xngnjfIlK4&list=WL&index=141&t=16s)
- [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=V4waklkBC38&list=WL&index=55&t=111s)
- [Terraform Full Course for Beginners](https://www.youtube.com/watch?v=EJ3N-hhiWv0&list=WL&index=39&t=27s)
- [KodeKloud -  Terraform for DevOps Beginners + Labs: Complete Step by Step Guide!](https://www.youtube.com/watch?v=YcJ9IeukJL8&list=WL&index=16&t=11s)
- [Terraform Simple Projects](https://terraform.joshuajebaraj.com/)
- [Terraform Tutorial - The Best Project Ideas](https://www.youtube.com/watch?v=oA-pPa0vfks)
- [Awesome Terraform](https://github.com/shuaibiyy/awesome-terraform)

[第61天](day61.md)见。
