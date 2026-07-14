---
title: '#90DaysOfDevOps - Kubernetes 应用部署 - 第54天'
published: false
description: 90DaysOfDevOps - Kubernetes 应用部署
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048764
---
## Kubernetes 应用部署

现在我们终于要开始将一些应用程序部署到我们的集群中了，有人会说这就是 Kubernetes 存在的原因——用于应用交付。

这里的想法是，我们可以获取我们的容器镜像，然后将它们作为 Pod 部署到我们的 Kubernetes 集群中，以利用 Kubernetes 作为容器编排器的优势。

### 将应用部署到 Kubernetes

我们有几种方式可以将应用程序部署到 Kubernetes 集群中，我们将介绍两种最常见的方法：YAML 文件和 Helm Chart。

我们将使用 minikube 集群进行这些应用部署。我们将逐步介绍一些之前提到的 Kubernetes 组件或构建块。

在整个章节和容器章节中，我们都讨论了镜像以及 Kubernetes 的好处，以及我们如何在这个平台上轻松处理扩展。

在第一步中，我们将简单地在 minikube 集群中创建一个无状态应用。我们将在第一个演示中使用事实上的标准无状态应用 `nginx`，我们将配置一个 Deployment，它将为我们提供 Pod，然后我们还将创建一个 Service，它将允许我们导航到由 nginx Pod 托管的简单 Web 服务器。所有这些都包含在一个 Namespace 中。

![](Images/Day54_Kubernetes1.png)

### 创建 YAML

在第一个演示中，我们希望用 YAML 定义所有内容。我们本可以有一个关于 YAML 的完整章节，但我将略过这一点，并在最后留下一些更详细地介绍 YAML 的资源。

我们可以将以下内容作为一个 YAML 文件创建，也可以将其分解为应用程序的每个方面，即为 Namespace、Deployment 和 Service 创建创建单独的文件，但在下面的文件中，我们使用 `---` 在一个文件中分隔这些部分。你可以 [在这里](2022\Days\Kubernetes\nginx-stateless-demo.yaml) 找到这个文件。

```
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
  "labels": {
    "name": "nginx"
  }
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: nginx
spec:
  selector:
    app: nginx-deployment
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
### 检查我们的集群

在部署任何东西之前，我们应该确保没有名为 `nginx` 的现有 Namespace。我们可以通过运行 `kubectl get namespace` 命令来做到这一点，如下所示，我们没有名为 `nginx` 的 Namespace。

![](Images/Day54_Kubernetes2.png)

### 部署我们的应用

现在我们已经准备好将应用程序部署到 minikube 集群，同样的过程也适用于任何其他 Kubernetes 集群。

我们需要导航到 YAML 文件的位置，然后我们可以运行 `kubectl create -f nginx-stateless-demo.yaml`，然后你会看到已经创建了 3 个对象：一个 Namespace、一个 Deployment 和一个 Service。

![](Images/Day54_Kubernetes3.png)

让我们再次运行命令来查看集群中的可用 Namespace：`kubectl get namespace`，你现在可以看到我们有了新的 Namespace。

![](Images/Day54_Kubernetes5.png)

如果我们然后使用 `kubectl get pods -n nginx` 检查 Namespace 中的 Pod，你会看到我们有 1 个处于就绪和运行状态的 Pod。

![](Images/Day54_Kubernetes4.png)

我们还可以通过运行 `kubectl get service -n nginx` 检查我们的 Service 是否已创建。

![](Images/Day54_Kubernetes6.png)

最后我们可以检查我们的 Deployment，Deployment 是我们保持期望配置的地方和方式。

![](Images/Day54_Kubernetes7.png)

上述操作需要几个值得了解的命令，但你也可以使用 `kubectl get all -n nginx` 来查看我们用那一个 YAML 文件部署的所有内容。

![](Images/Day54_Kubernetes8.png)

你会注意到在上图中我们还有一个 ReplicaSet，在我们的 Deployment 中我们定义了希望部署多少个镜像副本。这最初设置为 1，但如果我们想快速扩展我们的应用程序，我们可以通过几种方式来实现。

我们可以使用 `kubectl edit deployment nginx-deployment -n nginx` 编辑我们的文件，这将在终端中打开一个文本编辑器，使你能够修改你的 Deployment。

![](Images/Day54_Kubernetes9.png)

如果在终端内的文本编辑器中保存上述内容后没有问题且使用了正确的格式，那么你应该会在 Namespace 中看到额外部署的内容。

![](Images/Day54_Kubernetes10.png)

我们还可以使用 kubectl 和 `kubectl scale deployment nginx-deployment --replicas=10 -n nginx` 来更改副本数量。

![](Images/Day54_Kubernetes11.png)

如果我们愿意，我们同样可以使用此方法将应用程序缩减回 1。我使用了 edit 选项，但你也可以使用上面的 scale 命令。

![](Images/Day54_Kubernetes12.png)

希望在这里你不仅能看到用例，而且事物启动和关闭的速度非常快，我们还有能力快速上下扩展我们的应用程序。如果这是一个 Web 服务器，我们可以在繁忙时段扩展，在负载较小时缩减。


### 暴露我们的应用

但是我们如何访问我们的 Web 服务器呢？

如果你查看上面的 Service，你会发现没有可用的 External IP，所以我们不能仅仅打开 Web 浏览器就期望它能神奇地访问。对于访问，我们有几个选项。

**ClusterIP** - 你看到的 IP 是 ClusterIP，它位于集群的内部网络上。只有集群内部的东西才能访问这个 IP。

**NodePort** - 使用 NAT 在集群中每个选定节点的相同端口上暴露 Service。

**LoadBalancer** - 在当前云中创建一个外部负载均衡器，我们使用的是 minikube，但如果你构建了自己的 Kubernetes 集群（即我们在 VirtualBox 中所做的），你需要在集群中部署一个 LoadBalancer（例如 MetalLB）来提供此功能。

**Port-Forward** - 我们还有端口转发的能力，它允许你从本地主机访问和与内部 Kubernetes 集群进程进行交互。实际上，此选项仅用于测试和故障排查。

我们现在有几个选项可供选择，Minikube 有一些限制或者说我应该称之为与完整 Kubernetes 集群的差异。

我们可以简单地运行以下命令，使用我们的本地工作站来端口转发我们的访问。

`kubectl port-forward deployment/nginx-deployment -n nginx 8090:80`

![](Images/Day54_Kubernetes13.png)

注意，当你运行上述命令时，这个终端现在无法使用，因为这充当了你到本地机器和端口的端口转发。

![](Images/Day54_Kubernetes14.png)

我们现在将专门介绍如何在 Minikube 中暴露我们的应用程序。我们还可以使用 minikube 创建一个 URL 来连接到 Service [更多详情](https://minikube.sigs.k8s.io/docs/commands/service/)

首先，我们将使用 `kubectl delete service nginx-service -n nginx` 删除我们的 Service。

接下来，我们将使用 `kubectl expose deployment nginx-deployment --name nginx-service --namespace nginx --port=80 --type=NodePort` 创建一个新的 Service，注意这里我们将使用 expose 并将类型更改为 NodePort。

![](Images/Day54_Kubernetes15.png)

最后，在一个新的终端中运行 `minikube --profile='mc-demo' service nginx-service --url -n nginx` 来为我们的 Service 创建一个隧道。

![](Images/Day54_Kubernetes16.png)

打开浏览器或在终端中按住 control 键点击链接。

![](Images/Day54_Kubernetes17.png)

### Helm

Helm 是另一种部署应用程序的方式。被称为 "Kubernetes 的包管理器"，你可以在 [这里](https://helm.sh/) 了解更多。

Helm 是 Kubernetes 的包管理器。Helm 可以被认为是 Kubernetes 版的 yum 或 apt。Helm 部署 Chart，你可以将其视为打包的应用程序。它是预配置应用资源的蓝图，可以作为一个易于使用的 Chart 部署。然后你可以使用另一组配置部署 Chart 的另一个版本。

他们有一个网站，你可以浏览所有可用的 Helm Chart，当然你也可以创建自己的。文档也很清晰简洁，不像我第一次在这个领域听到 helm 这个词时那样令人生畏。

让 Helm 启动和运行或安装非常简单。你可以在这里找到几乎所有发行版的二进制文件和下载链接，包括你的 RaspberryPi arm64 设备。

或者你可以使用安装脚本，这里的好处是将会下载并安装最新版本的 Helm。

```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3

chmod 700 get_helm.sh

./get_helm.sh
```

最后，还可以选择使用应用程序的包管理器：mac 的 homebrew、windows 的 chocolatey、Ubuntu/Debian 的 apt、snap 和 pkg。

到目前为止，Helm 似乎是在集群中下载和安装不同测试应用程序的首选方式。

这里有一个很好的资源值得链接：[ArtifactHUB](https://artifacthub.io/)，这是一个查找、安装和发布 Kubernetes 包的平台。我还要给 [KubeApps](https://kubeapps.com/) 一个推荐，它是一个展示 Helm Chart 的 UI。

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

[第55天](day55.md)见
