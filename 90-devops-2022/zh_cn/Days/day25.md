---
title: '#90DaysOfDevOps - 用 Python 做网络自动化 - 第 25 天'
published: false
description: 90DaysOfDevOps - 用 Python 做网络自动化
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049038
---
## 用 Python 做网络自动化

Python 是用于自动化网络运维的标准语言。

虽然 Python 不只用于网络自动化，但当你在寻找相关资料时，它几乎无处不在。正如前面提到的，如果不是 Python，那通常就是 Ansible，而 Ansible 也是用 Python 编写的。

我想我之前已经提过，在“学习一门编程语言”那一节里，我选择了 Golang 而不是 Python，原因是我们公司正好在用 Go 开发，所以这对我来说是一个很好的学习理由；如果不是这样，那段时间本来会用来学 Python。

- 可读性和易用性 - Python 看起来就是很顺手。代码里似乎也不需要用 `{}` 来标记代码块的开始和结束。再配合像 VS Code 这样强大的 IDE，你在运行 Python 代码时会有一个相当轻松的起步。

Pycharm 也可能是另一个值得提到的 IDE。

- 库 - Python 的可扩展性才是真正的宝藏。我之前提过，它不仅适用于网络自动化，实际上，各种设备和配置都有大量对应库。你可以在这里看到庞大的库数量：[PyPI](https://pypi.python.org/pypi)

当你想把某个库下载到工作站时，可以使用一个叫做 `pip` 的工具连接到 PyPI，并将其下载到本地。像 Cisco、Juniper 和 Arista 这样的网络厂商也开发了库，方便访问它们的设备。

- 强大且高效 - 还记得在 Go 那几天里，我们做“Hello World”示例时好像写了 6 行代码吗？在 Python 里只需要：

``` 
print('hello world')
```

把上面这些点放在一起，就不难理解为什么在做自动化时，Python 通常被称为事实标准工具。

我觉得有必要指出，几年前可能就已经有脚本与网络设备交互，用来自动备份配置，或者收集日志和其他设备洞察。我们这里谈到的自动化稍有不同，因为整体网络环境也已经发生变化，更适合这种思路，也支持更多自动化。

- 软件定义网络 - SDN 控制器负责将控制平面配置下发到网络中的所有设备，这意味着任何网络变更只需要一个接触点，不再需要逐台设备进行 telnet 或 SSH，也不再依赖人工逐个操作，从而避免反复出现故障或配置错误的可能。

- 高层编排 - 在这些 SDN 控制器之上再上一个层级，就可以实现服务级别的编排，并将这一编排层集成到你选择的平台中，例如 VMware、Kubernetes、公有云等。

- 基于策略的管理 - 你想要什么？目标状态是什么？你只需要把它描述出来，系统就会自行处理细节，最终让环境达到目标状态。

## 搭建实验环境

并不是每个人都能接触到实体路由器、交换机和其他网络设备。

我希望大家既能了解前面提到的一些工具，也能亲自动手，学习如何自动化网络配置。

在选择方面，我们可以考虑几种方案。

- [GNS3 VM](https://www.gns3.com/software/download-vm)
- [Eve-ng](https://www.eve-ng.net/)
- [Unimus](https://unimus.net/) 这不是实验环境，但概念很有意思。

我们将使用 [Eve-ng](https://www.eve-ng.net/) 来搭建实验室。前面提过，你也可以使用实体设备，但老实说，虚拟环境意味着我们可以拥有一个沙盒环境来测试许多不同场景。另外，能够接触不同设备和拓扑也会很有意思。

我们将使用 EVE-NG 社区版来完成所有操作。

### 开始上手

社区版提供 ISO 和 OVF 两种格式供[下载](https://www.eve-ng.net/index.php/download/)

我们将使用 OVF 版本，不过使用 ISO 的话，也可以直接在裸机服务器上部署，而不需要虚拟化管理程序。

![](Images/Day25_Networking1.png)

在这个演示里，我们会使用 VMware Workstation，因为我通过 vExpert 拿到了许可证。不过你同样可以使用 VMware Player，或者使用[文档](https://www.eve-ng.net/index.php/documentation/installation/system-requirement/)中提到的其他选项。不幸的是，我们不能使用之前用过的 VirtualBox！

这也是我在 GNS3 和 VirtualBox 组合上遇到问题的地方，尽管它们是受支持的。

[下载 VMware Workstation Player - 免费](https://www.vmware.com/uk/products/workstation-player.html)

[VMware Workstation PRO](https://www.vmware.com/uk/products/workstation-pro.html) 另外也要注意，它提供免费的试用期！

### 在 VMware Workstation PRO 上安装

现在我们已经下载并安装好了虚拟化软件，也下载好了 EVE-NG 的 OVF 文件。如果你使用的是 VMware Player，请告诉我这个流程是否相同。

现在我们可以开始配置了。

打开 VMware Workstation，然后选择 `File` 和 `Open`。

![](Images/Day25_Networking2.png)

当你下载 EVE-NG OVF 镜像时，它会被放在一个压缩文件里。把内容解压到对应文件夹中，整理成类似这样的结构。

![](Images/Day25_Networking3.png)

导航到你下载 EVE-NG OVF 镜像的位置，然后开始导入。

给它起一个容易识别的名字，并将虚拟机保存到系统中的某个位置。

![](Images/Day25_Networking4.png)

导入完成后，将处理器数量增加到 4，并将分配的内存调到 8 GB。（如果使用最新版本，导入后通常就应该是这个设置；如果不是，就手动编辑虚拟机设置。）

另外，确保勾选 `Virtualize Intel VT-x/EPT or AMD-V/RVI` 选项。这个选项会指示 VMware Workstation 将虚拟化标志传递给客户机操作系统（嵌套虚拟化）。这正是我在 GNS3 和 VirtualBox 中遇到的问题，尽管我的 CPU 是支持的。

![](Images/Day25_Networking5.png)

### 开机与访问

顺带一提和一个小坑：还记得我说过这在 VirtualBox 上不可用吗？结果我在 VMware Workstation 和 EVE-NG 上也遇到了同样的问题，但这并不是虚拟化平台本身的错！

我的 Windows 机器上运行着 WSL2，这似乎会移除在环境中运行嵌套虚拟化的能力。我也不太明白为什么 Ubuntu 虚拟机还能运行，因为使用 WSL2 时，CPU 的 Intel VT-d 虚拟化能力似乎会被占用掉。

要解决这个问题，我们可以在 Windows 机器上运行下面的命令并重启系统。注意，在这个功能关闭期间，你将无法使用 WSL2。

`bcdedit /set hypervisorlaunchtype off`

当你想恢复使用 WSL2 时，需要运行这条命令并重启。

`bcdedit /set hypervisorlaunchtype auto`

这两个命令都应该以管理员身份运行！

好了，回到正题。现在你应该已经在 VMware Workstation 中启动了一台机器，并且会看到类似这样的提示界面。

![](Images/Day25_Networking6.png)

在上面的提示中，你可以使用：

username = root
password = eve

随后系统会再次要求你输入 root 密码，这个密码之后会用于 SSH 登录主机。

接着我们可以修改主机名。

![](Images/Day25_Networking7.png)

接下来，我们定义一个 DNS 域名。我用了下面这个，但我不确定后面是否还需要修改。

![](Images/Day25_Networking8.png)

然后我们配置网络，我选择静态地址，这样分配到的 IP 在重启后仍然保持不变。

![](Images/Day25_Networking9.png)

最后一步，从你工作站可达的网络中提供一个静态 IP 地址。

![](Images/Day25_Networking10.png)

这里还有一些额外步骤，你需要提供子网掩码、默认网关和 DNS。

完成后系统会重启，启动后你就可以把静态 IP 地址输入到浏览器中。

![](Images/Day25_Networking11.png)

GUI 的默认用户名是 `admin`，密码是 `eve`；SSH 的默认用户名是 `root`，密码也是 `eve`，不过如果你在安装过程中修改过，这些值就会不同。

![](Images/Day25_Networking12.png)

我选择了 HTML5 控制台而不是原生控制台，因为在切换不同控制台时，它会在浏览器中打开一个新标签页。

接下来我们将要：

- 安装 EVE-NG 客户端包
- 将一些网络镜像加载到 EVE-NG 中
- 构建网络拓扑
- 添加节点
- 连接节点
- 开始编写 Python 脚本
- 了解 telnetlib、Netmiko、Paramiko 和 Pexpect

## 资源

- [Free Course: Introduction to EVE-NG](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - Creating your first lab](https://www.youtube.com/watch?v=9dPWARirtK8)
- [3 Necessary Skills for Network Automation](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [Computer Networking full course](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [Practical Networking](http://www.practicalnetworking.net/)
- [Python Network Automation](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我们在 [第 26 天](day26.md) 再见
