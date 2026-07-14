---
title: '#90DaysOfDevOps - 数据与应用移动性 - 第90天'
published: false
description: "90DaysOfDevOps - 数据与应用移动性"
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048748
---
## 数据与应用移动性

#90DaysOfDevOps 挑战的第90天！在最后一节课程中，我将介绍我们的数据和应用程序的移动性。我将专门聚焦于 Kubernetes，但跨平台以及平台之间的移动性是一个不断增长的需求，并且在实际领域中很常见。

用例是"我想将我的工作负载、应用程序和数据从一个位置移动到另一个位置"，原因有很多，可能是成本、风险，或者是为了向业务提供更好的服务。

在本节课程中，我们将获取我们的工作负载，并研究如何将 Kubernetes 工作负载从一个集群移动到另一个集群，但在这样做的过程中，我们将改变我们的应用程序在目标位置的表现方式。

事实上，它使用了很多我们在 [灾难恢复](day89.md) 中介绍的特性。

### **需求**

我们当前的 Kubernetes 集群无法处理需求，我们的成本正在飙升，这是一个业务决策，我们希望将我们的生产 Kubernetes 集群迁移到我们的灾难恢复位置，位于不同的公有云上，这将提供扩展的能力，而且价格更便宜。我们还可以利用目标云中一些原生的云服务。

我们当前的关键任务应用程序（PacMan）有一个数据库（MongoDB），并且运行在慢速存储上，我们希望迁移到更新更快的存储层。

当前的 PacMan（NodeJS）前端扩展性不佳，我们希望在新位置增加可用 pod 的数量。

### 付诸实践

我们有了需求，事实上我们的导入已经到达了灾难恢复 Kubernetes 集群。

我们需要做的第一个工作是删除我们在第89天为灾难恢复测试进行的恢复操作。

我们可以在 "standby" minikube 集群上使用 `kubectl delete ns pacman` 来完成此操作。

![](Images/Day90_Data1.png)

首先，进入 Kasten K10 仪表板，选择 Applications（应用程序）卡片。从下拉菜单中选择 "Removed"（已移除）。

![](Images/Day90_Data2.png)

然后我们会得到可用恢复点的列表。我们将选择我们拥有的那个，因为它包含我们的关键任务数据。

![](Images/Day90_Data3.png)

当我们进行灾难恢复流程时，我们将所有内容保留为默认值，但如果你有一个需要对应用程序进行转换的灾难恢复流程，这些选项是存在的。在这种情况下，我们有更改存储和副本数量的需求。

![](Images/Day90_Data4.png)

选择 "Apply transforms to restored resources"（对恢复的资源应用转换）选项。

![](Images/Day90_Data5.png)

碰巧的是，我们想要执行的转换的两个内置示例正好符合我们的需求。

![](Images/Day90_Data6.png)

第一个需求是，在我们的主集群上，我们使用的 Storage Class 叫做 `csi-hostpath-sc`，而在我们的新集群中，我们希望使用 `standard`，因此我们可以在这里进行更改。

![](Images/Day90_Data7.png)

看起来不错，点击底部的创建转换按钮。

![](Images/Day90_Data8.png)

下一个需求是我们希望将 Pacman 前端 deployment 扩展到 "5"。

![](Images/Day90_Data9.png)

如果你正在跟随操作，你应该会看到我们的两个转换，如下所示。

![](Images/Day90_Data10.png)

你可以从下面的图片中看到，我们将恢复下面列出的所有制品，如果我们愿意，我们也可以对我们想要恢复的内容进行精细化控制。点击 "Restore"（恢复）按钮。

![](Images/Day90_Data11.png)

同样，我们会被要求确认操作。

![](Images/Day90_Data12.png)

最后要展示的是，现在如果我们回到终端并查看我们的集群，你可以看到我们有5个 pacman pod，我们的 storageclass 现在设置为 standard 而不是 csi-hostpath-sc。

![](Images/Day90_Data13.png)

通过转换可以实现许多不同的选项。这不仅适用于迁移，还适用于灾难恢复、测试和开发类型的场景等等。

### API 和自动化

我没有谈到利用 API 和自动化其中一些任务的能力，但它们是存在的，整个 UI 中都有线索，提供了命令集，以便利用 API 进行自动化任务。

关于 Kasten K10 需要注意的重要一点是，部署时它被部署在 Kubernetes 集群内部，然后可以通过 Kubernetes API 进行调用。

这使我们结束了关于存储和保护数据的部分。

## 相关资料

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

### **结语**

当我结束这个挑战时，我想继续征求反馈，以确保信息始终相关。

我也意识到有很多话题我无法涵盖，或者无法更深入地探讨 DevOps 相关主题。

这意味着我们明年可以再次尝试这个挑战，并找到另外90天的内容和演练来学习。

### 接下来是什么？

首先，暂时停止写作，我在2022年1月1日开始了这个挑战，并在2022年3月31日19:50 BST完成了它！这是一段艰苦的旅程，但正如我所说，并且我已经说了很长时间，如果这个内容能帮助到一个人，那么公开学习总是值得的！

我有一些关于下一步走向的想法，希望它能在 GitHub 仓库之外有生命，我们可以考虑创建一本电子书，甚至可能是一本实体书。

我也知道我们需要检查一下，确保在实现任何类似目标之前，所有内容在语法上都是正确的。如果有人知道如何将 markdown 转换为印刷或电子书，将非常感激你的反馈。

一如既往地保持 issues 和 PRs 的提交。

谢谢！
@MichaelCade1
