---
title: "#90DaysOfDevOps - 动手实践备份与恢复 - 第87天"
published: false
description: "90DaysOfDevOps - 动手实践备份与恢复"
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048717
---
## 动手实践备份与恢复

在上一节课程中，我们接触了 [Kopia](https://kopia.io/)，一个我们用来将重要数据备份到本地 NAS 和云端对象存储的开源备份工具。

在本节中，我想进入 Kubernetes 备份的世界。这是我们在挑战早期在 [Kubernetes 全景图](Days/day49.md) 中涵盖的一个平台。

我们将再次使用我们的 minikube 集群，但这次我们将利用一些可用的插件。

### Kubernetes 集群设置

要设置我们的 minikube 集群，我们将执行 `minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p 90daysofdevops --kubernetes-version=1.21.2`，你会注意到我们使用了 `volumesnapshots` 和 `csi-hostpath-driver`，因为我们在进行备份时将充分利用这些功能。

在这一点上，我知道我们还没有部署 Kasten K10，但我们希望在集群启动后执行以下命令，我们要对 volumesnapshotclass 进行注解，以便 Kasten K10 可以使用它。

```
kubectl annotate volumesnapshotclass csi-hostpath-snapclass \
    k10.kasten.io/is-snapshot-class=true
```

我们还要使用以下命令将默认的 storageclass 从标准的默认 storageclass 更改为 csi-hostpath storageclass。

```
kubectl patch storageclass csi-hostpath-sc -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'

kubectl patch storageclass standard -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'
```

![](Images/Day87_Data1.png)

### 部署 Kasten K10

添加 Kasten Helm 仓库

`helm repo add kasten https://charts.kasten.io/`

我们也可以在这里使用 `arkade kasten install k10`，但为了演示的目的，我们将运行以下步骤。[更多详情](https://blog.kasten.io/kasten-k10-goes-to-the-arkade)

创建命名空间并部署 K10，注意这将花费大约 5 分钟

`helm install k10 kasten/k10 --namespace=kasten-io --set auth.tokenAuth.enabled=true --set injectKanisterSidecar.enabled=true --set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true --create-namespace`

![](Images/Day87_Data1.png)

你可以通过运行以下命令来观察 pod 启动。

`kubectl get pods -n kasten-io -w`

![](Images/Day87_Data3.png)

端口转发以访问 K10 仪表板，打开一个新终端来运行以下命令

`kubectl --namespace kasten-io port-forward service/gateway 8080:8000`

Kasten 仪表板将可在以下地址访问：`http://127.0.0.1:8080/k10/#/`

![](Images/Day87_Data4.png)

要登录仪表板，我们现在需要一个令牌，我们可以通过以下命令获取。

```
TOKEN_NAME=$(kubectl get secret --namespace kasten-io|grep k10-k10-token | cut -d " " -f 1)
TOKEN=$(kubectl get secret --namespace kasten-io $TOKEN_NAME -o jsonpath="{.data.token}" | base64 --decode)

echo "Token value: "
echo $TOKEN
```

![](Images/Day87_Data5.png)

现在我们拿着这个令牌输入到浏览器中，然后会提示你输入电子邮件和公司名称。

![](Images/Day87_Data6.png)

然后我们就可以访问 Kasten K10 仪表板了。

![](Images/Day87_Data7.png)

### 部署我们的有状态应用程序

使用我们在 Kubernetes 部分中使用过的有状态应用程序。

![](Images/Day55_Kubernetes1.png)

你可以在这里找到此应用程序的 YAML 配置文件 [pacman-stateful-demo.yaml](Days/Kubernetes/pacman-stateful-demo.yaml)

![](Images/Day87_Data8.png)

我们可以使用 `kubectl get all -n pacman` 来检查我们的 pod 是否正在启动。

![](Images/Day87_Data9.png)

在一个新终端中，我们可以然后端口转发 pacman 前端。`kubectl port-forward svc/pacman 9090:80 -n pacman`

在浏览器中打开另一个标签页，访问 http://localhost:9090/

![](Images/Day87_Data10.png)

花些时间在后台的 MongoDB 数据库中刷新一些高分记录。

![](Images/Day87_Data11.png)

### 保护我们的高分记录

现在我们的数据库中有一些关键任务数据，我们不希望丢失它。我们可以使用 Kasten K10 来保护整个应用程序。

如果我们回到 Kasten K10 仪表板标签页，你会看到我们的应用程序数量已经从 1 增加到 2，这是因为我们向 Kubernetes 集群添加了 pacman 应用程序。

![](Images/Day87_Data12.png)

如果你点击 "Applications"（应用程序）卡片，你会看到我们集群中自动发现的应用程序。

![](Images/Day87_Data13.png)

使用 Kasten K10，我们能够利用基于存储的快照，以及将我们的副本导出到对象存储选项。

为了演示的目的，我们将在集群中创建一个手动存储快照，然后我们可以向我们的高分记录中添加一些恶意数据，以模拟意外错误或不是吗？

首先，我们可以使用下面的手动快照选项。

![](Images/Day87_Data14.png)

为了演示，我将所有内容保留为默认值

![](Images/Day87_Data15.png)

回到仪表板，你会得到作业运行时的状态报告，完成后它应该看起来和这个一样成功。

![](Images/Day87_Data16.png)

### 故障场景

我们现在可以通过简单地对我们的应用程序进行一个预设的恶意更改，来对我们的关键任务数据做出致命的更改。

如下所示，我们有两个输入，我们可能不希望它们出现在我们的生产关键任务数据库中。

![](Images/Day87_Data17.png)

### 恢复数据

显然这是一个简单的演示，在某种程度上并不现实，但你见过删除数据库有多容易吗？

现在我们希望让那个高分列表看起来更干净，恢复到错误发生之前的样子。

回到 "Applications"（应用程序）卡片，在 pacman 标签页上，我们现在有 1 个恢复点可以用来恢复。

![](Images/Day87_Data18.png)

当你选择恢复时，你可以看到与该应用程序关联的所有快照和导出。

![](Images/Day87_Data19.png)

选择该恢复选项，侧边窗口将出现，我们将保留默认设置并点击恢复。

![](Images/Day87_Data20.png)

确认你确实想要执行此操作。

![](Images/Day87_Data21.png)

然后你可以回到仪表板查看恢复的进度。你应该会看到类似这样的内容。

![](Images/Day87_Data22.png)

但更重要的是，我们的关键任务应用程序中的高分列表看起来如何。你需要重新启动到 pacman 的端口转发，就像我们之前介绍的那样。

![](Images/Day87_Data23.png)

这是一个超级简单的演示，只是触及了 Kasten K10 在备份方面真正能做到的皮毛。未来我将创建一些更深入的视频内容来介绍这些领域。我们还将使用 Kasten K10 来强调数据管理方面的其他重要领域，如灾难恢复和数据移动性。

接下来我们将看看应用程序一致性。

## 相关资料

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

[第88天](day88.md)见。
