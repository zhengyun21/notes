---
title: '#90DaysOfDevOps - 搭建实验室 - 第 26 天'
published: false
description: 90DaysOfDevOps - 搭建实验室
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048762
---
## 搭建实验室

我们将继续使用 EVE-NG 搭建模拟网络，希望能把一些设备部署起来，并开始思考如何自动化这些设备的配置。在 [第 25 天](day25.md) 我们已经讲过如何使用 VMware Workstation 在机器上安装 EVE-NG。

### 安装 EVE-NG 客户端

还有一个客户端包，可以让我们在通过 SSH 连接设备时选择使用哪个应用程序。它还会为链路间的数据包抓取配置 Wireshark。你可以下载适用于你操作系统的客户端包（Windows、macOS、Linux）。

[EVE-NG Client Download](https://www.eve-ng.net/index.php/download/)

![](Images/Day26_Networking1.png)

小提示：如果你在 Linux 上作为客户端，那么可以使用这个[客户端包](https://github.com/SmartFinn/eve-ng-integration)。

安装过程就是一路“下一步、下一步”，我建议保持默认设置。

### 获取网络镜像

这一步比较有挑战。我参考了一些视频，最后会附上链接。这些资源会告诉我们路由器和交换机镜像的下载位置，以及如何、在哪里上传它们。

需要注意的是，我这里使用这些内容完全是出于学习目的。我建议你从网络厂商处下载官方镜像。

[Blog & Links to YouTube videos](https://loopedback.com/2019/11/15/setting-up-eve-ng-for-ccna-ccnp-ccie-level-studies-includes-multiple-vendor-node-support-an-absolutely-amazing-study-tool-to-check-out-asap/) 

[How To Add Cisco VIRL vIOS image to Eve-ng](https://networkhunt.com/how-to-add-cisco-virl-vios-image-to-eve-ng/)

整体来说，这里的步骤有点复杂，其实可以更简单一些，但上面的博客和视频已经完整演示了如何把镜像添加到你的 EVE-NG 设备中。

我使用 FileZilla 通过 SFTP 将 qcow2 文件传输到虚拟机里。

对于我们的实验室，我们需要 Cisco vIOS L2（交换机）和 Cisco vIOS（路由器）。

### 创建实验室

在 EVE-NG 的 Web 界面中，我们将创建新的网络拓扑。我们会有四台交换机和一台路由器，它将充当我们通往外部网络的网关。

| Node        | IP Address  |
| ----------- | ----------- |
| Router      | 10.10.88.110|
| Switch1     | 10.10.88.111|
| Switch2     | 10.10.88.112|
| Switch3     | 10.10.88.113|
| Switch4     | 10.10.88.114|

#### 将节点添加到 EVE-NG

当你第一次登录 EVE-NG 时，会看到如下界面，我们先从创建第一个实验室开始。

![](Images/Day26_Networking2.png)

给实验室起个名字，其他字段都是可选的。

![](Images/Day26_Networking3.png)

随后你会看到一个空白画布，可以开始创建网络。右键单击画布，然后选择添加节点。

接下来你会看到一长串节点选项。如果你按照前面的步骤操作，下面蓝色标出的两个选项应该可用，其余选项则会是灰色且不可选。

![](Images/Day26_Networking4.png)

我们要向实验室中添加以下内容：

- 1 台 Cisco vIOS 路由器
- 4 台 Cisco vIOS 交换机

按照简单的向导把它们添加到实验室中，最后应该会像下面这样。

![](Images/Day26_Networking5.png)

#### 连接节点

现在我们需要在路由器和交换机之间建立连接。方法很简单，把鼠标悬停在设备上，看到下面这样的连接图标后，再把它连接到你想连接的另一台设备上即可。

![](Images/Day26_Networking6.png)

当你完成环境连接后，也可以通过右键菜单中的方框或圆形来定义物理边界或位置。你还可以添加文本，这在我们想定义实验室中的命名或 IP 地址时很有用。

我继续把自己的实验室整理成了下面这样。

![](Images/Day26_Networking7.png)

你还会注意到，上面的实验室当前都是关闭状态。我们可以全选后右键单击，然后选择 `start selected` 来启动实验室。

![](Images/Day26_Networking8.png)

当实验室运行起来后，你就可以连接到每台设备的控制台，这时你会发现它们还很“空白”，几乎没有任何配置。我们可以通过复制配置，或者在每个终端里自己创建配置，来为每个节点添加一些内容。

我会把我的配置留在仓库的 `Networking` 文件夹里供参考。

| 节点 | 配置 |
| ----------- | ----------- |
| 路由器 | [R1](Networking/R1) |
| 交换机 1 | [SW1](Networking/SW1) |
| 交换机 2 | [SW2](Networking/SW2) |
| 交换机 3 | [SW3](Networking/SW3) |
| 交换机 4 | [SW4](Networking/SW4) |

## 资源

- [Free Course: Introduction to EVE-NG](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - Creating your first lab](https://www.youtube.com/watch?v=9dPWARirtK8)
- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

由于我不是网络工程师，这里使用的大多数示例都来自这本内容很丰富的书。它不是免费的，但我借用其中一些场景来帮助理解网络自动化。

- [Hands-On Enterprise Automation with Python（书籍）](https://www.packtpub.com/product/hands-on-enterprise-automation-with-python/9781788998512)

我们在 [第 27 天](day27.md) 再见
