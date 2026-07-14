---
title: '#90DaysOfDevOps - 备份所有平台 - 第86天'
published: false
description: 90DaysOfDevOps - 备份所有平台
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049058
---
## 备份所有平台

在整个挑战过程中，我们讨论了许多不同的平台和环境。所有这些平台都有一个共同点：它们都需要一定程度的数据保护！

数据保护已经存在很多很多年了，但我们今天拥有的海量数据以及这些数据带来的价值意味着，我们必须确保不仅通过拥有多个节点和跨应用程序的高可用性来应对基础设施故障，还必须考虑我们需要将这些重要数据的安全副本存放在一个安全可靠的位置，以防故障情况发生。

如今我们似乎经常听到关于网络犯罪和勒索软件的消息，不要误会我的意思，这是一个巨大的威胁，我坚持认为你会遭到勒索软件攻击。这不是是否会发生的问题，而是何时会发生的问题。因此，确保你的数据安全以备不时之需就更加重要了。然而，数据丢失最常见的原因不是勒索软件或网络犯罪，而仅仅是意外删除！

我们都经历过，删除了不该删除的东西，然后瞬间后悔。

借助我们在挑战过程中讨论的所有技术和自动化手段，保护任何有状态数据或甚至复杂的无状态配置的需求仍然存在，无论平台如何。

![](Images/Day86_Data1.png)

但我们应该能够在考虑到自动化的情况下执行数据保护，并能够集成到我们的工作流中。

如果我们看看什么是备份：

*在信息技术中，备份或数据备份是获取计算机数据副本并将其存储在其他地方，以便在数据丢失事件后用于恢复原始数据。动词形式，指执行此操作的过程，是 "back up"，而名词和形容词形式是 "backup"。*

如果将其分解为最简单的形式，备份就是将数据复制并粘贴到新位置。简单来说，我现在就可以进行备份，只需将文件从 C: 盘复制到 D: 盘，这样如果 C: 盘出了问题或者文件中被错误地编辑了，我就可以恢复到 D: 盘上的副本。现在，如果我的电脑坏了，C 盘和 D 盘都无法使用，那么我就没有受到保护，所以我必须考虑一个解决方案，或者将数据副本存放在系统之外，也许放在家里的 NAS 设备上？但如果我的房子出了什么事，也许我需要考虑将数据存储在另一个地点的另一个系统上，也许云是一个选择。也许我可以将重要文件的副本存储在多个位置，以降低故障风险？

### 3-2-1 备份方法论

现在似乎是谈论 3-2-1 规则或备份方法论的好时机。我实际上做过一个[闪电演讲](https://www.youtube.com/watch?v=5wRt1bJfKBw)来涵盖这个话题。

我们之前已经提到了一些为什么需要保护数据的极端情况，但下面还列出了更多：

![](Images/Day86_Data2.png)

这让我可以谈论 3-2-1 方法论。我的第一份数据副本或备份应该尽可能接近我的生产系统，原因是基于恢复速度，并且回到最初关于意外删除的观点，这将是最常见的恢复原因。但我希望将其存储在原始系统或生产系统之外的合适第二介质上。

然后我们要确保还将数据副本发送到外部或异地，这就是第二个地点的作用，无论是另一栋房子、建筑物、数据中心还是公有云。

![](Images/Day86_Data3.png)

### 备份责任

关于不需要备份的说法，我们可能已经听过所有的迷思了，比如 "一切都是无状态的"，我的意思是如果一切都是无状态的，那么业务是什么？没有数据库？没有 Word 文档？显然，企业中的每个人都有责任确保他们受到保护，但为关键任务应用程序和数据提供备份流程的责任很可能最终落到运维团队身上。

另一个很好的说法是 "高可用性就是我的备份，我们在集群中构建了多个节点，这不可能宕机！"，但当你对数据库犯了错误，并且这个错误被复制到集群中的所有节点时，或者发生火灾、洪水或流血事件导致集群不再可用，重要数据也随之丢失时，情况就不是这样了。这不是固执的问题，而是对数据和服务的认知问题，每个人都绝对应该将高可用性和容错能力纳入他们的架构中，但这并不能替代备份的需求！

复制似乎也能为我们提供数据的异地副本，也许上面提到的那个集群确实跨越多个位置，然而第一次意外的错误仍然会被复制到那里。但同样，备份需求应该与应用程序复制或系统复制并存于环境中。

话虽如此，你也可以走向另一个极端，将数据副本发送到太多位置，这不仅会增加成本，还会增加被攻击的风险，因为你的攻击面现在大大扩展了。

无论如何，谁来负责备份？这在每个企业中都会不同，但应该有人主动承担起了解备份需求的责任。同时还要了解恢复计划！

### 没人在意，直到每个人都在意

备份就是一个典型的例子，在需要恢复某些东西之前，没人在意备份。除了备份数据的需求之外，我们还需要考虑如何恢复！

以我们的文本文档为例，我们谈论的是非常小的文件，因此来回复制的能力是简单且快速的。但如果我们谈论的是 100GB 以上的文件，那么这将需要时间。此外，我们还必须考虑我们需要恢复的粒度级别，以虚拟机为例。

我们有整个虚拟机，我们有操作系统，有应用程序安装，如果这是一个数据库服务器，那么我们还有一些数据库文件。如果我们犯了错误，在数据库中插入了错误的代码行，我可能不需要恢复整个虚拟机，我希望对恢复的内容进行精细化控制。

### 备份场景

我现在想开始构建一个保护某些数据的场景，具体来说，我想保护我本地机器上的一些文件（在本例中是 Windows，但我要使用的工具实际上不仅是免费开源的，而且是跨平台的），我希望确保它们被保护到我家里本地的 NAS 设备，同时也保护到云中的对象存储桶。

我想备份这些重要数据，它恰好是 90DaysOfDevOps 的仓库，是的，这也会被发送到 GitHub，你可能正在那里阅读这个，但如果我的机器坏了，GitHub 也宕机了呢？任何人都将如何能够阅读这些内容，同时我又如何能够将这些数据恢复到另一个服务上。

![](Images/Day86_Data5.png)

有很多工具可以帮助我们实现这一目标，但我将使用一个名为 [Kopia](https://kopia.io/) 的开源备份工具，它能够让我们在将备份发送到多个位置的同时进行加密、去重和压缩。

你可以在[这里](https://github.com/kopia/kopia/releases)找到下载版本，在撰写本文时，我将使用 v0.10.6。

### 安装 Kopia

Kopia 有 CLI 和 GUI 版本，我们将使用 GUI，但要知道你也可以使用 CLI 版本，适用于那些没有提供 GUI 的 Linux 服务器。

我将使用 `KopiaUI-Setup-0.10.6.exe`

非常快速地下一步下一步安装，然后当你打开应用程序时，你会看到选择你希望用作备份仓库的存储类型的选项。

![](Images/Day86_Data6.png)

### 设置仓库

首先，我们希望使用我们的本地 NAS 设备设置一个仓库，我们将使用 SMB 来完成此操作，但我相信也可以使用 NFS。

![](Images/Day86_Data7.png)

在下一个屏幕上，我们将定义一个密码，这个密码用于加密仓库内容。

![](Images/Day86_Data8.png)

现在我们已经配置好了仓库，我们可以触发一个临时快照（adhoc snapshot）来开始将数据写入其中。

![](Images/Day86_Data9.png)

首先，我们需要输入我们想要快照的路径，在我们的例子中，我们想要复制我们的 `90DaysOfDevOps` 文件夹。我们稍后会回到调度方面的内容。

![](Images/Day86_Data10.png)

我们可以定义快照保留策略。

![](Images/Day86_Data11.png)

也许有一些文件或文件类型是我们希望排除的。

![](Images/Day86_Data12.png)

如果我们想要定义一个计划，我们可以在这个屏幕上完成，当你第一次创建这个快照时，这是定义它的起始页面。

![](Images/Day86_Data13.png)

你会看到还有许多其他设置可以在这里处理。

![](Images/Day86_Data14.png)

选择 "snapshot now"（立即快照），数据将被写入你的仓库。

![](Images/Day86_Data15.png)

### 异地备份到 S3

使用 Kopia，通过 UI 似乎一次只能配置一个仓库。但通过 UI，我们可以发挥创意，基本上拥有多个仓库配置文件可供选择，以实现我们在对象存储中拥有本地和异地副本的目标。

我选择将数据发送到的对象存储是 Google Cloud Storage。我首先登录到我的 Google Cloud Platform 账户并创建了一个存储桶。我已经在我的系统上安装了 Google Cloud SDK，但运行 `gcloud auth application-default login` 让我通过了我的账户认证。

![](Images/Day86_Data16.png)

然后我使用 Kopia 的 CLI 来显示我在上一步添加了 SMB 仓库后当前仓库的状态。我使用的是 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository status` 命令。

![](Images/Day86_Data17.png)

现在我们已经准备好替换仓库的配置以进行演示，如果我们想要一个长期解决方案来同时命中这两个仓库，我们可能会创建一个 `smb.config` 文件和一个 `object.config` 文件，并能够运行这两个命令将我们的数据副本发送到每个位置。要添加我们的仓库，我们运行了 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository create gcs --bucket 90daysofdevops`

上面的命令假设我们创建的 Google Cloud Storage 存储桶名为 `90daysofdevops`

![](Images/Day86_Data18.png)

现在我们已经创建了新仓库，我们可以再次运行 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config repository status` 命令，现在将显示 GCS 仓库配置。

![](Images/Day86_Data19.png)

接下来我们需要做的是创建一个快照并将其发送到我们新创建的仓库。使用 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config kopia snapshot create "C:\Users\micha\demo\90DaysOfDevOps"` 命令，我们可以启动这个过程。你可以在下面的浏览器中看到，我们的 Google Cloud Storage 存储桶现在根据我们的备份放置了 kopia 文件。

![](Images/Day86_Data20.png)

通过上述流程，我们能够满足将重要数据发送到 2 个不同位置的需求，其中 1 个是异地的 Google Cloud Storage，当然我们的生产数据副本仍然在不同的介质类型上。

### 恢复

恢复是另一个需要考虑的方面，而且非常重要，Kopia 使我们不仅能够恢复到现有位置，还能恢复到新位置。

如果我们运行命令 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config snapshot list`，这将列出我们当前在配置的仓库（GCS）中的快照。

![](Images/Day86_Data21.png)

然后我们可以使用 `"C:\Program Files\KopiaUI\resources\server\kopia.exe" --config-file=C:\Users\micha\AppData\Roaming\kopia\repository.config mount all Z:` 命令直接从 GCS 挂载这些快照。

![](Images/Day86_Data22.png)

我们还可以使用 `kopia snapshot restore kdbd9dff738996cfe7bcf99b45314e193` 来恢复快照内容。

显然，上面的命令非常长，这是因为我使用的是 KopiaUI 版本的 kopia.exe，正如在演练顶部解释的那样，你可以下载 kopia.exe 并将其放入路径中，这样你就可以直接使用 `kopia` 命令。

在下一节课程中，我们将专注于保护 Kubernetes 中的工作负载。

## 相关资料

- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

[第87天](day87.md)见。
