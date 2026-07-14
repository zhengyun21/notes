---
title: '#90DaysOfDevOps - Kubernetes与多环境管理 - 第61天'
published: false
description: 90DaysOfDevOps - Kubernetes与多环境管理
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048743
---
## Kubernetes与多环境管理

到目前为止，在本节关于基础设施即代码（Infrastructure as Code）的内容中，我们已经了解了如何部署虚拟机——虽然是部署到VirtualBox中，但原理是一样的：我们在代码中定义虚拟机应该是什么样子的，然后进行部署。Docker容器也是如此，在本节中我们将看看Terraform如何与Kubernetes支持的资源进行交互。

我一直在使用Terraform在三大主要云提供商上部署我的Kubernetes集群用于演示目的，你可以在仓库[tf_k8deploy](https://github.com/MichaelCade/tf_k8deploy)中找到相关代码。

然而，你也可以使用Terraform与Kubernetes集群中的对象进行交互，这可以通过[Kubernetes provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs)来实现，或者也可以使用[Helm provider](https://registry.terraform.io/providers/hashicorp/helm/latest)来管理你的chart部署。

现在我们可以像在前面章节中展示的那样使用`kubectl`。但在你的Kubernetes环境中使用Terraform也有一些好处。

- 统一的工作流——如果你使用terraform来部署集群，你可以使用相同的工作流和工具在Kubernetes集群内部进行部署

- 生命周期管理——Terraform不仅仅是一个配置工具，它还能实现变更、更新和删除。

### 简单的Kubernetes演示

就像上一节中我们创建的演示一样，我们现在可以将nginx部署到我们的Kubernetes集群中，这里我将再次使用minikube进行演示。我们创建我们的Kubernetes.tf文件，你可以在[文件夹](2022/Days/IaC/Kubernetes/kubernetes.tf)中找到它。

在该文件中，我们将定义我们的Kubernetes提供程序（provider），指向我们的kubeconfig文件，创建一个名为nginx的命名空间（namespace），然后创建一个包含2个副本（replicas）的部署（deployment），最后是一个服务（service）。

```
terraform {
  required_providers {
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = ">= 2.0.0"
    }
  }
}
provider "kubernetes" {
  config_path = "~/.kube/config"
}
resource "kubernetes_namespace" "test" {
  metadata {
    name = "nginx"
  }
}
resource "kubernetes_deployment" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    replicas = 2
    selector {
      match_labels = {
        app = "MyTestApp"
      }
    }
    template {
      metadata {
        labels = {
          app = "MyTestApp"
        }
      }
      spec {
        container {
          image = "nginx"
          name  = "nginx-container"
          port {
            container_port = 80
          }
        }
      }
    }
  }
}
resource "kubernetes_service" "test" {
  metadata {
    name      = "nginx"
    namespace = kubernetes_namespace.test.metadata.0.name
  }
  spec {
    selector = {
      app = kubernetes_deployment.test.spec.0.template.0.metadata.0.labels.app
    }
    type = "NodePort"
    port {
      node_port   = 30201
      port        = 80
      target_port = 80
    }
  }
}
```

我们在新的项目文件夹中首先要做的是运行`terraform init`命令。

![](Images/Day61_IAC1.png)

然后在运行`terraform apply`命令之前，让我向你展示我们还没有任何命名空间。

![](Images/Day61_IAC2.png)

当我们运行apply命令时，它将在我们的Kubernetes集群中创建这3个新资源：命名空间、部署和服务。

![](Images/Day61_IAC3.png)

我们现在可以查看集群中已部署的资源。

![](Images/Day61_IAC4.png)

因为我们使用的是minikube，你在前面的章节中已经看到过，当我们尝试使用docker网络来处理ingress时，它有其自身的局限性。但如果我们简单地执行`kubectl port-forward -n nginx svc/nginx 30201:80`命令，并打开浏览器访问 http://localhost:30201/ ，我们应该能看到我们的NGINX页面。

![](Images/Day61_IAC5.png)

如果你想尝试更多Terraform与Kubernetes的详细演示，那么[HashiCorp Learn站点](https://learn.hashicorp.com/tutorials/terraform/kubernetes-provider)非常适合学习。


### 多环境管理

如果我们想运行任何我们经历过的演示，但现在希望有特定的生产（production）、预发布（staging）和开发（development）环境，并且它们看起来完全一样，利用这段代码来实现，那么使用Terraform有两种方法

- `terraform workspaces`——在一个后端（backend）中有多个命名部分

- 文件结构——目录布局提供隔离，模块（modules）提供复用。

上述每种方法都有其优缺点。

### terraform workspaces

优点
- 易于上手
- 便捷的terraform.workspace表达式
- 最小化代码重复

缺点
- 容易出错（我们正是试图通过使用TF来消除这一点）
- 状态存储在同一个后端中
- 代码库不能明确地展示部署配置。

### 文件结构

优点
- 后端的隔离
    - 提高安全性
    - 降低人为错误的可能性
- 代码库完全代表已部署的状态

缺点
- 需要多次terraform apply来配置环境
- 更多的代码重复，但可以通过模块来最小化。

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

[第62天](day62.md)见。
