---
title: '#90DaysOfDevOps - Rancher 概览 - 动手实践 - 第53天'
published: false
description: 90DaysOfDevOps - Rancher 概览 - 动手实践
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048742
---
## Rancher 概览 - 动手实践

在本节中，我们将了解 Rancher。到目前为止，我们所做的一切都是在命令行中使用 kubectl 完成的，但我们有一些非常优秀的 UI 和多集群管理工具，可以为我们的运维团队提供良好的集群管理可见性。

根据 Rancher [官网](https://rancher.com/) 的介绍：

*Rancher 是一个完整的软件栈，面向采用容器的团队。它解决了在任何基础设施上管理多个 Kubernetes 集群的运营和安全挑战，同时为 DevOps 团队提供运行容器化工作负载的集成工具。*

Rancher 使我们能够从几乎任何位置部署生产级的 Kubernetes 集群，然后提供集中式的身份验证、访问控制和可观测性。我在前面一节中提到过，当涉及到 Kubernetes 以及应该在何处或可以在何处运行它们时，选择几乎是压倒性的，但看看 Rancher，它们在哪里其实并不重要。

### 部署 Rancher

我们需要做的第一件事是在本地工作站上部署 Rancher，有多种方式和位置可以选择来进行这一步，对我来说，我想使用我的本地工作站，并将 Rancher 作为 Docker 容器运行。通过运行下面的命令，我们将拉取一个容器镜像，然后就可以访问 Rancher UI。

其他 Rancher 部署方法可参考 [Rancher 快速入门指南](https://rancher.com/docs/rancher/v2.6/en/quick-start-guide/deployment/)

`sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher`

正如你在 Docker Desktop 中看到的那样，我们有一个正在运行的 Rancher 容器。

![](Images/Day53_Kubernetes1.png)

### 访问 Rancher UI

随着上述容器的运行，我们应该能够通过网页访问它。`https://localhost` 将显示如下登录页面。

![](Images/Day53_Kubernetes2.png)

按照以下说明获取所需的密码。因为我使用的是 Windows，所以我选择使用 Windows 上的 bash，因为需要使用 grep 命令。

![](Images/Day53_Kubernetes3.png)

然后我们可以使用上述密码登录，下一页是定义新密码的地方。

![](Images/Day53_Kubernetes4.png)

完成上述步骤后，我们将登录并看到欢迎界面。作为 Rancher 部署的一部分，我们还会看到一个本地 K3s 集群被自动配置。

![](Images/Day53_Kubernetes5.png)

### Rancher 快速导览

我们首先要看的是本地部署的 K3S 集群。你可以在下图中很好地看到集群内部正在发生的事情。这是默认部署，我们尚未向该集群部署任何内容。你可以看到它由 1 个节点组成，有 5 个 Deployment。然后你还可以看到有关 Pod、核心和内存的一些统计数据。

![](Images/Day53_Kubernetes6.png)

在左侧菜单中，我们还有 "Apps & Marketplace"（应用与市场）选项卡，这允许我们选择要在集群上运行的应用程序。如前所述，Rancher 赋予我们运行或管理多个不同集群的能力。通过应用市场，我们可以非常轻松地部署我们的应用程序。

![](Images/Day53_Kubernetes7.png)

另一件值得一提的事情是，如果你确实需要访问 Rancher 管理的任何集群，在右上角你可以打开一个针对所选集群的 kubectl shell。

![](Images/Day53_Kubernetes8.png)

### 创建新集群

在过去的两节课中，我们在本地创建了一个 minikube 集群，并使用 Vagrant 和 VirtualBox 创建了一个 3 节点的 Kubernetes 集群。使用 Rancher，我们也可以创建集群。在 [Rancher 文件夹](Days/Kubernetes/Rancher) 中，你会发现额外的 vagrant 文件，它们将构建相同的 3 个节点，但不包含创建 Kubernetes 集群的步骤（我们希望 Rancher 为我们完成这个）。

不过，我们确实需要安装 Docker 并更新操作系统，所以你仍然会看到 `common.sh` 脚本在每个节点上运行。这也将安装 Kubeadm、Kubectl 等。但它不会运行 Kubeadm 命令来创建和加入我们的节点到集群中。

我们可以导航到我们的 vagrant 文件夹位置，然后简单地运行 `vagrant up`，这将开始在我们的 virtualbox 中创建 3 个虚拟机的过程。

![](Images/Day53_Kubernetes9.png)

现在我们的节点或虚拟机已经就位并准备就绪，然后我们可以使用 Rancher 来创建我们的新 Kubernetes 集群。创建集群的第一个界面会给你一些选项，选择你的集群所在的位置，即你是否在使用公共云托管的 Kubernetes 服务、vSphere 或其他东西。

![](Images/Day53_Kubernetes10.png)

我们将选择 "custom"（自定义），因为我们没有使用其中一个集成平台。打开页面是定义集群名称的地方（下面显示的是 local，但你不能使用 local，我们的集群名为 vagrant）。你可以在这里定义 Kubernetes 版本、网络提供商以及一些其他配置选项来启动和运行你的 Kubernetes 集群。

![](Images/Day53_Kubernetes11.png)

下一页将为你提供注册码，需要在每个节点上运行，并启用相应的服务：etcd、controlplane 和 worker。对于我们的主节点，我们需要 etcd 和 controlplane，因此命令如下所示。

![](Images/Day53_Kubernetes12.png)

```
sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run  rancher/rancher-agent:v2.6.3 --server https://10.0.0.1 --token mpq8cbjjwrj88z4xmf7blqxcfmwdsmq92bmwjpphdkklfckk5hfwc2 --ca-checksum a81944423cbfeeb92be0784edebba1af799735ebc30ba8cbe5cc5f996094f30b --etcd --controlplane
```

如果网络配置正确，你应该很快就能在 Rancher 仪表板中看到以下内容，表明第一个主节点正在注册，集群正在创建。

![](Images/Day53_Kubernetes13.png)

然后我们可以对每个工作节点重复注册过程，使用以下命令，一段时间后，你的集群将启动并运行，并能够利用应用市场来部署你的应用程序。

```
sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run  rancher/rancher-agent:v2.6.3 --server https://10.0.0.1 --token mpq8cbjjwrj88z4xmf7blqxcfmwdsmq92bmwjpphdkklfckk5hfwc2 --ca-checksum a81944423cbfeeb92be0784edebba1af799735ebc30ba8cbe5cc5f996094f30b --worker
```

![](Images/Day53_Kubernetes14.png)

在过去的 3 节课中，我们用了几种不同的方式来启动和运行 Kubernetes 集群，在接下来的几天里，我们将研究平台的应用程序方面—— arguably 最重要的部分。我们将研究 Service（服务）以及如何在 Kubernetes 中配置和使用我们的服务。

之后有人告诉我，引导 Rancher 节点的要求是那些虚拟机需要有 4GB 内存，否则它们会崩溃循环，我已经更新了配置，因为我们的工作节点之前只有 2GB。

### 我们将在 Kubernetes 系列中涵盖的内容

我们已经开始涵盖下面提到的一些内容，但我们明天将通过第二个集群部署进行更多实践，然后我们就可以开始将应用程序部署到我们的集群中。

- Kubernetes 架构
- Kubectl 命令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes Service（服务）
- Helm 包管理器
- 持久化存储
- 有状态应用

## 资源

如果你有使用过的免费资源，请随时通过 PR 添加到本仓库中，我很乐意将它们包含进来。

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

[第54天](day54.md)见
