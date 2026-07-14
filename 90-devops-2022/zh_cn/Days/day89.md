---
title: '#90DaysOfDevOps - 灾难恢复 - 第89天'
published: false
description: 90DaysOfDevOps - 灾难恢复
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048718
---
## 灾难恢复

我们已经提到过，不同的故障场景需要不同的恢复要求。当涉及到火灾、洪水和流血事件场景时，我们可以将这些主要视为灾难情况，在这种情况下，我们可能需要尽快将工作负载恢复并在完全不同的位置运行，或者至少具有接近零的恢复时间目标（RTO）。

只有通过自动化将完整的应用程序堆栈复制到备用环境，才能大规模实现这一点。

这允许在云区域、云提供商或本地和云基础设施之间进行快速故障转移。

延续至今的主题，我们将集中讨论如何使用 Kasten K10 来实现这一点，使用我们几节课程前部署和配置的 minikube 集群。

然后我们将创建另一个也安装了 Kasten K10 的 minikube 集群，作为我们的备用集群，理论上它可以位于任何位置。

Kasten K10 还具有内置功能，以确保如果运行它的 Kubernetes 集群发生问题，目录数据将被复制并在新的集群中可用 [K10 灾难恢复](https://docs.kasten.io/latest/operating/dr.html)。

### 向 K10 添加对象存储

我们需要做的第一件事是添加一个对象存储桶作为我们备份的目标位置。这不仅作为一个异地位置，而且我们还可以利用它作为我们的灾难恢复源数据来从中恢复。

我已经清空了我们上一节课程中为 Kanister 演示创建的 S3 存储桶。

![](Images/Day89_Data1.png)

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

现在我们回到了 Kasten K10 仪表板，我们可以添加我们的位置配置文件，在页面顶部选择 "Settings"（设置），然后选择 "New Profile"（新建配置文件）。

![](Images/Day89_Data2.png)

从下面的图片中你可以看到，当涉及到此位置配置文件的位置时，我们有选择，我们将选择 Amazon S3，并添加我们的敏感访问凭证、区域和存储桶名称。

![](Images/Day89_Data3.png)

如果在新建配置文件创建窗口向下滚动，你还会看到我们有能力启用不可变备份，它利用 S3 对象锁定 API。对于这个演示，我们不会使用它。

![](Images/Day89_Data4.png)

点击 "Save Profile"（保存配置文件），你现在可以看到我们新创建或添加的位置配置文件，如下所示。

![](Images/Day89_Data5.png)

### 创建策略以保护 pacman 应用程序到对象存储

在上一节课程中，我们只创建了 pacman 应用程序的临时快照，因此我们需要创建一个备份策略，将我们的应用程序备份发送到我们新创建的对象存储位置。

如果你回到仪表板并选择 Policy（策略）卡片，你会看到如下所示的屏幕。选择 "Create New Policy"（创建新策略）。

![](Images/Day89_Data6.png)

首先，我们可以给我们的策略一个有用的名称和描述。我们还可以定义备份频率，为了演示目的，我使用的是按需（on-demand）。

![](Images/Day89_Data7.png)

接下来，我们希望通过快照导出启用备份，这意味着我们想要将数据发送到我们的位置配置文件。如果你有多个，你可以选择要将备份发送到哪一个。

![](Images/Day89_Data8.png)

接下来，我们通过名称或标签选择应用程序，我将按名称选择并选择所有资源。

![](Images/Day89_Data9.png)

在高级设置下，我们不会使用这些设置，但基于我们昨天对 Kanister 的演练，我们也可以利用 Kanister 作为 Kasten K10 的一部分来进行那些应用程序一致的数据副本。

![](Images/Day89_Data10.png)

最后选择 "Create Policy"（创建策略），你现在会在策略窗口中看到该策略。

![](Images/Day89_Data11.png)

在创建的策略底部，你会有 "Show import details"（显示导入详情），我们需要这个字符串才能将其导入到我们的备用集群中。暂时将它复制到安全的地方。

![](Images/Day89_Data12.png)

在继续之前，我们只需要选择 "run once"（运行一次），以将备份发送到我们的对象存储桶。

![](Images/Day89_Data13.png)

只是展示备份和导出数据的成功。

![](Images/Day89_Data14.png)


### 创建新的 minikube 集群并部署 K10

然后我们需要部署第二个 Kubernetes 集群，虽然这可以是任何受支持的 Kubernetes 版本，包括 OpenShift，但为了教育目的，我们将使用完全免费的 minikube 版本，但使用不同的名称。

使用 `minikube start --addons volumesnapshots,csi-hostpath-driver --apiserver-port=6443 --container-runtime=containerd -p standby --kubernetes-version=1.21.2`，我们可以创建我们的新集群。

![](Images/Day89_Data15.png)

然后我们可以使用以下命令在此集群中部署 Kasten K10

`helm install k10 kasten/k10 --namespace=kasten-io --set auth.tokenAuth.enabled=true --set injectKanisterSidecar.enabled=true --set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true --create-namespace`

这将需要一段时间，但与此同时，我们可以使用 `kubectl get pods -n kasten-io -w` 来观察我们的 pod 达到运行状态的进度。

值得注意的是，因为我们使用的是 minikube，所以当我们运行导入策略时，我们的应用程序就会直接运行，我们的 storageclass 在这个备用集群上是相同的。然而，我们将在最后一节课程中介绍的内容是关于移动性和转换。

当 pod 启动并运行时，我们可以按照我们在另一个集群上的前几步中经历的步骤进行操作。

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

### 将 Pacman 导入新的 minikube 集群

在这一点上，我们现在能够在该备用集群中创建一个导入策略，并连接到对象存储备份，并确定我们想要什么以及希望它看起来如何。

首先我们添加我们在另一个集群上早先演练过的位置配置文件，这里展示了深色模式，以显示我们的生产系统和灾难恢复备用位置之间的区别。

![](Images/Day89_Data16.png)

现在我们回到仪表板并进入策略标签页来创建一个新策略。

![](Images/Day89_Data17.png)

按照下面的图片创建导入策略。完成后，我们可以创建策略。这里有一些选项可以在导入后恢复，有些人可能想要这个选项，这将在完成后进入我们的备用集群进行恢复。我们也有能力改变它的样子，并计划在 [第90天](day90.md) 中展示这一点。

![](Images/Day89_Data18.png)

我选择了按需导入，但你显然可以设置一个计划，决定你希望这个导入何时发生。正因为如此，我将运行一次。

![](Images/Day89_Data19.png)

你可以在下面看到成功的导入策略作业。

![](Images/Day89_Data20.png)

如果我们现在回到仪表板并进入 Applications（应用程序）卡片，然后点击你下面看到的 "Removed"（已移除）旁边的下拉菜单，你会在这里看到我们的应用程序。选择 Restore（恢复）。

![](Images/Day89_Data21.png)

在这里我们可以看到可用的恢复点，这是我们在主集群上针对我们的 PacMan 应用程序运行的备份作业。

![](Images/Day89_Data22.png)

我不会更改任何默认值，因为我想在下一节课程中更详细地介绍这一点。

![](Images/Day89_Data23.png)

当你点击 "Restore"（恢复）时，它会提示你进行确认。

![](Images/Day89_Data24.png)

我们可以在下面看到我们在备用集群中，如果我们检查我们的 pod，我们可以看到我们的应用程序正在运行。

![](Images/Day89_Data25.png)

然后我们可以端口转发（在现实中和实际生活中，你不需要这一步来访问应用程序，你会使用 ingress）。

![](Images/Day89_Data26.png)

接下来我们将看看应用程序移动性和转换。

## 相关资料

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

[第90天](day90.md)见。
