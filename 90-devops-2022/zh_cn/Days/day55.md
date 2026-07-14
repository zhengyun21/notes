---
title: '#90DaysOfDevOps - Kubernetes 中的状态与 Ingress - 第55天'
published: false
description: 90DaysOfDevOps - Kubernetes 中的状态与 Ingress
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048779
---
## Kubernetes 中的状态与 Ingress

在 Kubernetes 章节的最后部分，我们将了解状态（State）和 Ingress。

到目前为止，我们所说的一切都是关于无状态（stateless）的。无状态实际上是指我们的应用程序不关心它使用哪个网络，也不需要任何永久存储。而有状态应用（stateful apps），例如数据库，为了使这样的应用程序正常运行，你需要确保 Pod 可以通过一个不会改变的唯一标识相互访问（主机名、IP 等）。有状态应用的示例包括 MySQL 集群、Redis、Kafka、MongoDB 等。基本上，任何存储数据的应用程序都是有状态应用。

### 有状态应用

StatefulSet 代表一组具有唯一、持久标识和稳定主机名的 Pod，无论它们被调度到哪里，Kubernetes 都会维护这些标识和主机名。任何给定 StatefulSet Pod 的状态信息和其他弹性数据都保存在与该 StatefulSet 关联的持久磁盘存储中。

### Deployment vs StatefulSet

- 复制有状态应用更加困难。
- 在 Deployment 中复制我们的 Pod（无状态应用）是相同且可互换的。
- 以随机顺序创建带有随机哈希的 Pod。
- 一个 Service 负载均衡到任何 Pod。

当涉及到 StatefulSet 或有状态应用时，上述情况变得更加困难。

- 不能同时创建或删除。
- 不能随机寻址。
- 副本 Pod 不是相同的。

在我们的演示中你很快就会看到，每个 Pod 都有自己的身份。在无状态应用中，你会看到随机的名称，例如 `app-7469bbb6d7-9mhxd`，而有状态应用则更类似于 `mongo-0`，然后在扩展时会创建一个名为 `mongo-1` 的新 Pod。

这些 Pod 是从相同的规范创建的，但它们不是可互换的。每个 StatefulSet Pod 在重新调度时都有一个持久的标识符。这是必要的，因为当我们需要数据库等有状态工作负载时，我们需要对数据库进行读写操作，不能有两个 Pod 同时写入而彼此不知道，因为这会导致数据不一致。我们需要确保在任何给定时间只有一个 Pod 在向数据库写入，但我们可以有多个 Pod 读取该数据。

StatefulSet 中的每个 Pod 都可以访问自己的持久卷和数据库的副本以供读取，这会从主节点持续更新。同样有趣的是，每个 Pod 还会将其 Pod 状态存储在这个持久卷中，如果 `mongo-0` 死了，当一个新的 Pod 被配置时，它将接管存储在存储中的 Pod 状态。

简而言之；StatefulSet vs Deployment

- 可预测的 Pod 名称 = `mongo-0`
- 固定的独立 DNS 名称
- Pod 身份 - 保留状态、保留角色
- 复制有状态应用很复杂
  - 有很多事情你必须做：
      - 配置克隆和数据同步。
      - 使远程共享存储可用。
      - 管理和备份

### 持久卷 | 声明 | StorageClass

如何在 Kubernetes 中持久化数据？

我们在上面提到，当我们有一个有状态应用时，我们必须将状态存储在某个地方，这就是需要卷的地方。Kubernetes 本身并不提供开箱即用的持久化功能。

我们需要一个不依赖于 Pod 生命周期的存储层。该存储应该可以从我们所有的 Kubernetes 节点访问和可用。该存储也应该在 Kubernetes 集群之外，以便即使在 Kubernetes 集群崩溃时也能存活。

### 持久卷（Persistent Volume）

- 用于存储数据的集群资源（类似于 CPU 和 RAM）。
- 通过 YAML 文件创建
- 需要实际的物理存储（NAS）
- 与 Kubernetes 集群的外部集成
- 你可以在存储中拥有不同类型的存储。
- PV 不是 Namespace 限定的
- 本地存储可用，但它特定于集群中的一个节点
- 数据库持久化应使用远程存储（NAS）

### 持久卷声明（Persistent Volume Claim）

上面的持久卷可以存在并可用，但除非它被应用程序声明，否则它就没有被使用。

- 通过 YAML 文件创建
- 持久卷声明用于 Pod 配置中（volumes 属性）
- PVC 与 Pod 位于相同的 Namespace 中
- 卷被挂载到 Pod 中
- Pod 可以有多种不同的卷类型（ConfigMap、Secret、PVC）

另一种理解 PV 和 PVC 的方式是：

PV 由 Kubernetes 管理员创建
PVC 由用户或应用开发者创建

我们还有另外两种类型的卷，我们不会详细讨论，但值得一提：

### ConfigMap | Secret
- 你的 Pod 的配置文件。
- 你的 Pod 的证书文件。

### StorageClass

- 通过 YAML 文件创建
- 当 PVC 声明时动态配置持久卷
- 每个存储后端都有自己的配置器
- 存储后端在 YAML 中定义（通过 provisioner 属性）
- 抽象底层存储提供商
- 定义该存储的参数


### 实践演练

在昨天的课程中，我们介绍了创建无状态应用的过程，这里我们想做的是同样的事情，但我们要使用 minikube 集群来部署一个有状态工作负载。

关于 minikube 命令的回顾，我们使用以下命令来获得使用持久化的能力和插件：`minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p mc-demo --kubernetes-version=1.21.2`

此命令使用 csi-hostpath-driver，这就是我们获取 storageclass 的方式，稍后我会展示。

应用程序的构建如下所示：

![](Images/Day55_Kubernetes1.png)

你可以 [在这里](Days/Kubernetes/pacman-stateful-demo.yaml) 找到此应用程序的 YAML 配置文件。

### StorageClass 配置

不过在开始部署应用程序之前还有一步，那就是确保我们的 storageclass（csi-hostpath-sc）是默认的。我们首先可以通过运行 `kubectl get storageclass` 命令来检查，但开箱即用的 minikube 集群会显示 standard storageclass 为默认值，因此我们必须使用以下命令来更改。

第一个命令将使我们的 csi-hostpath-sc storageclass 成为默认值。

`kubectl patch storageclass csi-hostpath-sc -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'`

此命令将从 standard StorageClass 中移除默认注释。

`kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'`

![](Images/Day55_Kubernetes2.png)

我们的集群开始时没有 pacman Namespace。`kubectl get namespace`

![](Images/Day55_Kubernetes3.png)

然后我们将部署我们的 YAML 文件。`kubectl create -f pacman-stateful-demo.yaml` 你可以从这个命令中看到，我们正在 Kubernetes 集群中创建多个对象。

![](Images/Day55_Kubernetes4.png)

我们现在有了新创建的 Namespace。

![](Images/Day55_Kubernetes5.png)

然后你可以从下一张图片和命令 `kubectl get all -n pacman` 中看到，在我们的 Namespace 中发生了很多事情。我们有运行 NodeJS Web 前端的 Pod，有运行后端数据库的 MongoDB。有用于访问这些 Pod 的 pacman 和 MongoDB 的 Service。我们有 pacman 的 Deployment 和 MongoDB 的 StatefulSet。

![](Images/Day55_Kubernetes6.png)

我们还有我们的持久卷和持久卷声明，运行 `kubectl get pv` 将给我们非 Namespace 限定的持久卷，运行 `kubectl get pvc -n pacman` 将给我们 Namespace 限定的持久卷声明。

![](Images/Day55_Kubernetes7.png)

### 玩游戏 | 我的意思是访问我们的关键任务应用

因为我们使用的是 Minikube，正如在无状态应用中提到的，在访问我们的应用程序时有一些障碍需要克服。然而，如果我们的集群中有 ingress 或负载均衡器的访问权限，Service 会设置为自动从中获取 IP 以获得外部访问权限。（你可以在上面的 pacman Namespace 所有组件的图像中看到这一点）。

对于此演示，我们将使用端口转发方法来访问我们的应用程序。通过打开一个新终端并运行以下命令 `kubectl port-forward svc/pacman 9090:80 -n pacman`，然后打开浏览器，我们现在就可以访问我们的应用程序。如果你在 AWS 或特定位置运行此应用，它还会报告云和区域以及主机（等于你在 Kubernetes 中的 Pod），同样你可以回顾上面的截图看到这个 Pod 名称。

![](Images/Day55_Kubernetes8.png)

现在我们可以去创建一个高分，然后将其存储在我们的数据库中。

![](Images/Day55_Kubernetes9.png)

好的，我们有一个高分，但如果我们删除我们的 `mongo-0` Pod 会发生什么？通过运行 `kubectl delete pod mongo-0 -n pacman` 我可以删除它，如果你仍然在应用中，你会看到那个高分至少在几秒钟内不可用。

![](Images/Day55_Kubernetes10.png)

现在如果我回到游戏中，我可以创建一个新游戏并查看我的高分。你唯一能真正相信我的方法就是亲自尝试一下，并在社交媒体上分享你的高分！

![](Images/Day55_Kubernetes11.png)

使用 Deployment，我们可以使用上一节中介绍的命令来扩展它，但特别是这里，如果你想举办一个大型吃豆人派对，你可以使用 `kubectl scale deployment pacman --replicas=10 -n pacman` 来扩展它。

![](Images/Day55_Kubernetes12.png)


### Ingress 详解

在结束 Kubernetes 之前，我还想要触及 Kubernetes 的一个巨大方面，那就是 Ingress。

### 什么是 Ingress？

到目前为止，在我们的示例中，我们使用了端口转发或 Minikube 中的特定命令来访问我们的应用程序，但这在生产环境中是行不通的。我们希望有一种更好的方式来大规模访问我们的应用程序，为多个用户提供服务。

我们还谈到 NodePort 是一个选项，但同样，这应该仅用于测试目的。

Ingress 为我们提供了一种更好的暴露应用程序的方式，这允许我们在 Kubernetes 集群内定义路由规则。

对于 Ingress，我们将创建一个转发请求到我们应用程序内部 Service 的规则。

### 什么时候需要 Ingress？

如果你使用的是云提供商、托管的 Kubernetes 服务，它们很可能会有自己的 Ingress 选项供你的集群使用，或者它们会提供自己的负载均衡器选项。你不必自己实现这一点，这是托管 Kubernetes 的好处之一。

如果你正在运行自己的集群，那么你需要配置一个入口点。

### 在 Minikube 上配置 Ingress

在我特定的名为 mc-demo 的运行集群上，我可以运行以下命令来在我的集群上启用 Ingress。

`minikube --profile='mc-demo' addons enable ingress`

![](Images/Day55_Kubernetes13.png)

如果我们现在检查我们的 Namespace，你会看到我们有一个新的 ingress-nginx Namespace。`kubectl get ns`

![](Images/Day55_Kubernetes14.png)

现在我们必须创建我们的 Ingress YAML 配置来指向我们的 Pacman Service。我已将此文件添加到仓库中 [pacman-ingress.yaml](Days/Kubernetes/pacman-ingress.yaml)

然后我们可以使用 `kubectl create -f pacman-ingress.yaml` 在我们的 ingress Namespace 中创建它。

![](Images/Day55_Kubernetes15.png)

然后如果我们运行 `kubectl get ingress -n pacman`

![](Images/Day55_Kubernetes16.png)

然后我被告知，因为我们使用的是 Windows 上的 WSL2 中运行的 minikube，我们必须使用 `minikube tunnel --profile=mc-demo` 创建 minikube 隧道。

但我仍然无法访问 192.168.49.2 来玩我的吃豆人游戏。

如果有人能在 Windows 和 WSL 上让这个工作起来，我将不胜感激你的反馈。我将在仓库上提出一个问题，一旦我有时间和解决方案，我会回来处理它。

更新：我觉得这篇博客有助于确定这在 WSL 上不起作用的原因 [Configuring Ingress to run Minikube on WSL2 using Docker runtime](https://hellokube.dev/posts/configure-minikube-ingress-on-wsl2/)

## 资源

如果你有使用过的免费资源，请随时通过 PR 添加到本仓库中，我很乐意将它们包含进来。

- [Kubernetes StatefulSet simply explained](https://www.youtube.com/watch?v=pPQKAR1pA9U)
- [Kubernetes Volumes explained](https://www.youtube.com/watch?v=0swOh5C3OVM)
- [Kubernetes Ingress Tutorial for Beginners](https://www.youtube.com/watch?v=80Ew_fsV4rM)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

这结束了我们的 Kubernetes 章节，关于 Kubernetes 还有很多额外的内容可以涵盖，7 天给了我们基础知识，但有人在经历 [100DaysOfKubernetes](https://100daysofkubernetes.io/overview.html)，在那里你可以真正深入研究。

接下来，我们将研究基础设施即代码（Infrastructure as Code）以及它在 DevOps 视角下扮演的重要角色。

[第56天](day56.md)见
