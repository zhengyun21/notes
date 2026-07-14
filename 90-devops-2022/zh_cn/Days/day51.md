---
title: '#90DaysOfDevOps - 部署你的第一个 Kubernetes 集群 - 第51天'
published: false
description: 90DaysOfDevOps - 部署你的第一个 Kubernetes 集群
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048778
---
## 部署你的第一个 Kubernetes 集群

在这篇文章中，我们将使用 minikube 在我们的本地机器上启动并运行一个 Kubernetes 集群，这将为我们提供 Kubernetes 部分的基准集群，尽管我们稍后也会在 VirtualBox 中部署一个 Kubernetes 集群。选择这种方法而不是在公共云中启动托管 Kubernetes 集群的原因是，即使在免费层中，后者也会花钱，我在上一节 [第50天](day50.md) 分享了一些博客，如果你想搭建那个环境的话。

### 什么是 Minikube？

*"minikube 快速在 macOS、Linux 和 Windows 上搭建本地 Kubernetes 集群。我们自豪地专注于帮助应用程序开发人员和新的 Kubernetes 用户。"*

你可能不属于上述人群，但我发现 minikube 是一个很棒的小工具，如果你只是想以 Kubernetes 的方式测试一些东西，你可以轻松部署一个应用程序，而且它们有一些很棒的插件，我也会介绍。

首先，无论你的工作站操作系统是什么，你都可以运行 minikube。首先，前往[这里的项目页面](https://minikube.sigs.k8s.io/docs/start/)。你的第一个选择是安装方法。我没有使用这种方法，但你可能会选择与我不同的方式（我的方式即将介绍）。

如下所述，你需要有一个"容器或虚拟机管理器，例如：Docker、Hyperkit、Hyper-V、KVM、Parallels、Podman、VirtualBox 或 VMware"，这就是 MiniKube 将运行的地方，简单的选项是，除非仓库中另有说明，我使用的是 Docker。你可以使用以下[链接](https://docs.docker.com/get-docker/)在你的系统上安装 Docker。

![](Images/Day51_Kubernetes1.png)

### 我安装 minikube 和其他先决条件的方式……

我已经使用 arkade 一段时间了来获取所有那些 Kubernetes 工具和 CLI，你可以在这个 [GitHub 仓库](https://github.com/alexellis/arkade) 上看到 Arkade 的入门步骤。我在其他需要安装东西的博客文章中也提到过这个。只需敲击 arkade get，然后看看你的工具或 CLI 是否可用的简单性非常方便。在 Linux 部分，我们谈到了包管理器以及获取软件的流程，你可以将 Arkade 视为 Kubernetes 所有应用程序和 CLI 的那个市场。一个非常方便的工具，可以在你的系统上使用，用 Golang 编写，跨平台。

![](Images/Day51_Kubernetes2.png)

作为 arkade 中可用应用程序的长列表的一部分，minikube 是其中之一，所以只需一个简单的 `arkade get minikube` 命令，我们现在就在下载二进制文件，一切准备就绪。

![](Images/Day51_Kubernetes3.png)

我们还需要 kubectl 作为我们工具集的一部分，所以你也可以通过 arkade 获取它，或者我相信 minikube 文档会通过上面提到的 curl 命令将其作为一部分下载下来。我们将在文章的后面更多地介绍 kubectl。

### 启动并运行 Kubernetes 集群

对于这一节，我想介绍一下在本地机器上启动并运行 Kubernetes 集群时可用的选项。我们可以简单地运行以下命令，它就会为你启动一个集群。

minikube 在命令行上使用，简单来说，一旦你把所有东西都安装好了，你可以运行 `minikube start` 来部署你的第一个 Kubernetes 集群。你会看到下面 Docker Driver 是默认的，我们将在这里运行嵌套虚拟化节点。我在文章开头提到了其他可用的选项，当你想扩展这个本地 Kubernetes 集群需要是什么样子时，其他选项会很有帮助。

在这个实例中，一个 Minikube 集群将由一个 Docker 容器组成，它将控制平面节点和 worker 节点合在一个实例中。而通常情况下，你会将这些节点分开。我们将在下一节中介绍，我们仍然关注家庭实验室类型的 Kubernetes 环境，但更接近生产架构。

![](Images/Day51_Kubernetes4.png)

我已经提到过很多次了，我真的很喜欢 minikube，因为它有可用的插件，能够用一个简单的命令部署一个包含所有必需插件的集群，这真的帮助我每次都部署相同的必需设置。

下面你可以看到这些插件的列表，我通常使用 `csi-hostpath-driver` 和 `volumesnapshots` 插件，但你可以在下面看到长长的列表。当然，这些插件通常可以使用 Helm 部署，这也是我们稍后在 Kubernetes 部分会介绍的内容，但这让事情变得简单多了。

![](Images/Day51_Kubernetes5.png)

我还在我们的项目中定义了一些额外的配置，apiserver 设置为 6433 而不是随机的 API 端口，我还将容器运行时定义为 containerd，不过 docker 是默认的，CRI-O 也可用。我还设置了一个特定的 Kubernetes 版本。

![](Images/Day51_Kubernetes6.png)

现在我们准备好使用 minikube 部署我们的第一个 Kubernetes 集群了。不过我之前提到过，你还需要 `kubectl` 来与你的集群交互。你可以使用 arkade 命令 `arkade get kubectl` 来安装 kubectl。

![](Images/Day51_Kubernetes7.png)

或者你可以从以下链接跨平台下载：

- [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux)
- [macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos)
- [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows)

一旦你安装了 kubectl，我们就可以用一个简单的命令与我们的集群交互，比如 `kubectl get nodes`。

![](Images/Day51_Kubernetes8.png)

### 什么是 kubectl？

我们现在已经有了 minikube | Kubernetes 集群在运行，我已经让你安装了 Minikube，我至少解释了它的作用，但我还没有真正解释 kubectl 是什么以及它的作用。

kubectl 是一个 CLI，用于或允许你与 Kubernetes 集群交互，我们在这里使用它来与我们的 minikube 集群交互，但我们也会使用 kubectl 来与我们在公共云中的企业集群交互。

我们使用 kubectl 来部署应用程序、检查和管理集群资源。在 Kubernetes 官方文档中可以找到一个更好的 [kubectl 概述](https://kubernetes.io/docs/reference/kubectl/overview/)。

kubectl 与我们之前一篇文章中简要介绍的控制平面节点上的 API server 进行交互。

### kubectl 速查表

除了官方文档之外，我在查找 kubectl 命令时还发现我总是打开这个页面。[Unofficial Kubernetes](https://unofficial-kubernetes.readthedocs.io/en/latest/)

|列出资源               |                                           |
| ------------------------------ | ----------------------------------------- |
|kubectl get nodes               |列出集群中的所有节点                  |
|kubectl get namespaces          |列出集群中的所有命名空间             |
|kubectl get pods                |列出默认命名空间中的所有 Pod |
|kubectl get pods -n name        |列出 "name" 命名空间中的所有 Pod          |
|kubectl get pods -n name        |列出 "name" 命名空间中的所有 Pod          |

|创建资源              |                                           |
| ------------------------------ | ----------------------------------------- |
|kubectl create namespace name   |创建一个名为 "name" 的命名空间           |
|kubectl create -f [filename]    |从 JSON 或 YAML 文件创建资源 |

|编辑资源               |                                           |
| ------------------------------ | ----------------------------------------- |
|kubectl edit svc/servicename    |编辑一个服务                          |

|资源的更多详细信息        |                                                        |
| ------------------------------ | ------------------------------------------------------ |
|kubectl describe nodes          | 详细显示任意数量资源的状态 |

|删除资源                |                                                        |
| ------------------------------ | ------------------------------------------------------ |
|kubectl delete pod              | 删除资源，这可以来自 stdin 或文件       |

你会发现自己想知道一些 kubectl 命令的简写名称，例如 `-n` 是 `namespace` 的简写，这使得输入命令更容易，而且如果你在编写脚本，你可以拥有更整洁的代码。

| 简写           | 全名                    |
| -------------------- | ---------------------------- |
|  csr                 |  certificatesigningrequests  |
|  cs                  |  componentstatuses           |
|  cm                  |  configmaps                  |
|  ds                  |  daemonsets                  |
|  deploy              |  deployments                 |
|  ep                  |  endpoints                   |
|  ev                  |  events                      |
|  hpa                 |  horizontalpodautoscalers    |
|  ing                 |  ingresses                   |
|  limits              |  limitranges                 |
|  ns                  |  namespaces                  |
|  no                  |  nodes                       |
|  pvc                 |  persistentvolumeclaims      |
|  pv                  |  persistentvolumes           |
|  po                  |  pods                        |
|  pdb                 |  poddisruptionbudgets        |
|  psp                 |  podsecuritypolicies         |
|  rs                  |  replicasets                 |
|  rc                  |  replicationcontrollers      |
|  quota               |  resourcequotas              |
|  sa                  |  serviceaccounts             |
|  svc                 |  services                    |

最后要补充的是，我创建了另一个围绕 minikube 的项目，帮助我快速启动演示环境来展示数据服务并使用 Kasten K10 保护这些工作负载，[Project Pace](https://github.com/MichaelCade/project_pace) 可以在那里找到，非常欢迎你的反馈或互动，它还展示或包括了一些自动化部署 minikube 集群和创建不同数据服务应用程序的方法。

接下来，我们将进入使用 VirtualBox 将多个节点部署到虚拟机中，但我们会在那里按下简易按钮，就像我们在 Linux 部分所做的那样，我们使用 vagrant 快速启动机器并按照我们的意愿部署软件。

我在昨天的文章中添加了这个列表，是我做过的关于不同 Kubernetes 集群部署的演练博客。

- [Kubernetes playground – How to choose your platform](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-1)
- [Kubernetes playground – Setting up your cluster](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-2)
- [Getting started with Amazon Elastic Kubernetes Service (Amazon EKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-amazon-elastic-kubernetes-service-amazon-eks)
- [Getting started with Microsoft Azure Kubernetes Service (AKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-azure-kubernetes-service-aks)
- [Getting Started with Microsoft AKS – Azure PowerShell Edition](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-aks-azure-powershell-edition)
- [Getting started with Google Kubernetes Service (GKE)](https://vzilla.co.uk/vzilla-blog/getting-started-with-google-kubernetes-service-gke)
- [Kubernetes, How to – AWS Bottlerocket + Amazon EKS](https://vzilla.co.uk/vzilla-blog/kubernetes-how-to-aws-bottlerocket-amazon-eks)
- [Getting started with CIVO Cloud](https://vzilla.co.uk/vzilla-blog/getting-started-with-civo-cloud)
- [Minikube - Kubernetes Demo Environment For Everyone](https://vzilla.co.uk/vzilla-blog/project_pace-kasten-k10-demo-environment-for-everyone)

### 我们将在 Kubernetes 系列中涵盖的内容

我们已经开始介绍下面提到的一些内容，但明天我们将通过第二次集群部署进行更多的实践操作，然后我们就可以开始在集群中部署应用程序了。

- Kubernetes 架构
- Kubectl 命令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes 服务
- Helm 包管理器
- 持久化存储
- 有状态应用

## 资源

如果你有使用过的免费资源，请随时通过 PR 将它们添加到这里，我很乐意将它们包含进来。

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

[第52天](day52.md)见。
