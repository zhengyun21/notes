---
title: '#90DaysOfDevOps - Python 与网络实战 - 第27天'
published: false
description: 90DaysOfDevOps - Python 与网络实战
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048735
---
## Python 与网络实战

在网络基础知识的最后一个部分，我们将介绍一些自动化任务和工具，使用我们在 [第26天](day26.md) 创建的实验环境。

我们将使用 SSH 隧道从客户端连接到我们的设备，而不是 telnet。客户端和设备之间创建的 SSH 隧道是加密的。我们在 Linux 章节的 [第18天](day18.md) 也介绍过 SSH。

## 访问我们的虚拟仿真环境

为了与我们的交换机交互，我们需要在 EVE-NG 网络内部有一台工作站，你可以在那里部署一台安装了 Python 的 Linux 机器来执行自动化操作（[在 EVE-NG 中设置 Linux 的资源](https://www.youtube.com/watch?v=3Qstk3zngrY)），或者你可以像我一样，定义一个云以便从工作站访问。

![](Images/Day27_Networking3.png)

为此，我们在画布上右键单击，选择 network，然后选择 "Management(Cloud0)"，这将桥接到我们的家庭网络。

![](Images/Day27_Networking4.png)

然而，这个网络中没有任何东西，所以我们需要从新网络向每个设备添加连接。（我的网络知识还需要加强，我觉得你只需将下一步操作连接到顶部路由器，然后通过这一根线缆就能连接到网络的其余部分？）

然后我登录到每个设备，并为云进入的适用接口运行了以下命令。

```
enable
config t
int gi0/0
ip add dhcp 
no sh 
exit 
exit
sh ip int br
```

最后一步为我们提供了家庭网络的 DHCP 地址。我的设备网络列表如下：

| 节点        | IP 地址  | 家庭网络 IP  |
| ----------- | ----------- | -----------      |
| Router      | 10.10.88.110| 192.168.169.115  |
| Switch1     | 10.10.88.111| 192.168.169.178  |
| Switch2     | 10.10.88.112| 192.168.169.193  |
| Switch3     | 10.10.88.113| 192.168.169.125  |
| Switch4     | 10.10.88.114| 192.168.169.197  |

### SSH 连接到网络设备

有了上述配置，我们现在可以使用工作站连接到我们家庭网络上的设备。我使用的是 Putty，但也可以访问其他终端，比如 git bash，让我能够 SSH 到我们的设备。

下面你可以看到我们已经 SSH 连接到路由器设备。(R1)

![](Images/Day27_Networking5.png)

### 使用 Python 从设备收集信息

我们可以利用 Python 的第一个示例是从所有设备收集信息，特别是，我希望能连接到每个设备并运行一个简单的命令，为我提供接口配置和设置。我将这个脚本存放在这里 [netmiko_con_multi.py](Networking/netmiko_con_multi.py)。

现在当我运行这个脚本时，我可以看到我所有设备的每个端口配置。

![](Images/Day27_Networking6.png)

如果你有很多不同的设备，这可能会很方便，创建这一个脚本，让你可以在一个地方集中控制和快速了解所有的配置。

### 使用 Python 配置设备

上面很有用，但使用 Python 来配置我们的设备呢？在我们的场景中，`SW1` 和 `SW2` 之间有一个 trunk 端口，再次想象一下，如果要跨许多相同的交换机执行此操作，我们希望将其自动化，而不必手动连接到每个交换机来进行配置更改。

我们可以使用 [netmiko_sendchange.py](Networking/netmiko_sendchange.py) 来实现这一点。这将通过 SSH 连接并在我们的 `SW1` 上执行更改，这也会更改到 `SW2`。

![](Images/Day27_Networking7.png)

现在对于那些查看代码的人来说，你会看到消息出现并告诉我们 `sending configuration to device`，但没有确认这已经发生了，所以我们可以在脚本中添加额外的代码来在我们的交换机上执行检查和验证，或者我们可以修改之前的脚本来向我们展示这一点。[netmiko_con_multi_vlan.py](Networking/netmiko_con_multi_vlan.py)

![](Images/Day27_Networking8.png)

### 备份设备配置

另一个用例是捕获我们的网络配置并确保我们有备份，但同样，我们不想连接到我们网络上的每个设备，所以我们也可以使用 [backup.py](Networking/backup.py) 来自动化这一点。你还需要用要备份的 IP 地址填充 [backup.txt](Networking/backup.txt)。

运行你的脚本，你应该会看到类似下面的内容。

![](Images/Day27_Networking9.png)

那可能只是我在 Python 中写一个简单的打印脚本，所以我应该也给你看看备份文件。

![](Images/Day27_Networking10.png)

### Paramiko

一个广泛使用的用于 SSH 的 Python 模块。你可以在官方 GitHub 链接 [这里](https://github.com/paramiko/paramiko) 了解更多信息。

我们可以使用 `pip install paramiko` 命令安装此模块。

![](Images/Day27_Networking1.png)

我们可以通过进入 Python shell 并导入 paramiko 模块来验证安装。

![](Images/Day27_Networking2.png)

### Netmiko

netmiko 模块专门针对网络设备，而 paramiko 是一个更广泛的用于处理 SSH 连接的工具。

我们上面与 paramiko 一起使用的 Netmiko 可以使用 `pip install netmiko` 安装。

Netmiko 支持许多网络供应商和设备，你可以在 [GitHub 页面](https://github.com/ktbyers/netmiko#supports) 上找到支持的设备列表。

### 其他模块

还值得提及一些我们没机会了解的其他模块，但它们在网络自动化方面提供了更多的功能。

`netaddr` 用于处理和操作 IP 地址，安装同样很简单，使用 `pip install netaddr`。

你可能会发现自己想把很多交换机配置存储在 Excel 电子表格中，`xlrd` 将允许你的脚本读取 Excel 工作簿，并将行和列转换为矩阵。使用 `pip install xlrd` 来安装该模块。

我还有一些没机会研究的其他网络自动化用例，可以在 [这里](https://github.com/ktbyers/pynet/tree/master/presentations/dfwcug/examples) 找到。

我想这就结束了我们 #90DaysOfDevOps 的网络章节，网络是我很久没接触的一个领域，还有很多内容要涵盖，但我希望在我的笔记和整个过程中分享的资源之间，能对某些人有所帮助。

## 相关资料

- [免费课程：EVE-NG 简介](https://www.youtube.com/watch?v=g6B0f_E0NMg)
- [EVE-NG - 创建你的第一个实验环境](https://www.youtube.com/watch?v=9dPWARirtK8)
- [网络自动化的3项必要技能](https://www.youtube.com/watch?v=KhiJ7Fu9kKA&list=WL&index=122&t=89s)
- [计算机网络完整课程](https://www.youtube.com/watch?v=IPvYjXCsTg8)
- [实用网络](http://www.practicalnetworking.net/)
- [Python 网络自动化](https://www.youtube.com/watch?v=xKPzLplPECU&list=WL&index=126)

我在这里使用的大多数示例都来自这本内容丰富的书，它不是免费的，但我使用其中的一些场景来帮助理解网络自动化。

- [Python 企业自动化实战（书籍）](https://www.packtpub.com/product/hands-on-enterprise-automation-with-python/9781788998512)

[第28天](day28.md)见，我们将开始研究云计算，并对该主题以及可用的内容有一个良好的掌握和基础知识。
