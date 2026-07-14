---
title: '#90DaysOfDevOps - Ansible：入门 - 第64天'
published: false
description: '90DaysOfDevOps - Ansible：入门'
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048765
---
## Ansible：入门

我们在[昨天的全景图介绍](day63.md)中简要介绍了Ansible是什么。但在这里我们将获取更多相关信息。首先，Ansible来自RedHat。其次，它是无代理（agentless）的，通过SSH连接并运行命令。第三，它是跨平台的（Linux和macOS、WSL2）且开源的（也有付费的企业版选项）。Ansible采用推送配置的模式，与其他模式不同。

### Ansible安装

正如你所想象的，RedHat和Ansible团队在Ansible文档方面做得非常出色。这通常从安装步骤开始，你可以在[这里](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)找到。请记住，我们说Ansible是一种无代理自动化工具，该工具部署到一个被称为"控制节点（Control Node）"的系统上，从这个控制节点，它通过SSH管理机器和其他设备（可能是网络设备）。

上述链接的文档中确实指出，Windows操作系统不能用作控制节点。

对于我的控制节点，至少在这个演示中，我将使用我们很久以前在[Linux部分](day20.md)中创建的Linux虚拟机作为我的控制节点。

该系统运行的是Ubuntu，安装步骤只需要以下命令。

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
现在我们应该已经在控制节点上安装了Ansible，你可以通过运行`ansible --version`来检查，你应该会看到类似下面的内容。

![](Images/Day64_config1.png)

在我们开始控制环境中的其他节点之前，我们还可以通过在本地机器上运行命令来检查Ansible的功能。`ansible localhost -m ping`将使用一个[Ansible模块（Ansible Module）](https://docs.ansible.com/ansible/2.9/user_guide/modules_intro.html)，这是在许多不同系统上快速执行单个任务的方法。我的意思是，只有本地主机的话可能不太有趣，但想象一下，如果你想获取某些东西或确保所有系统都正常运行，而你有1000多台服务器和设备。

![](Images/Day64_config2.png)

或者模块在实际生活中的用途可能是类似`ansible webservers -m service -a "name=httpd state=started"`这样的命令，它会告诉我们所有的Web服务器是否都在运行httpd服务。我在上面的命令中略过了webservers这个术语。

### 主机清单（hosts）

就像我上面使用localhost在系统上运行一个简单的ping模块一样，我无法指定网络中的另一台机器，例如在我使用的环境中，运行VirtualBox的Windows主机有一个网络适配器，IP为10.0.0.1，但你可以看到下面，我可以通过ping到达它，但我无法使用Ansible来执行该任务。

![](Images/Day64_config3.png)

为了让我们指定我们想要用这些任务自动化的主机或节点，我们需要定义它们。我们可以通过导航到系统上的/etc/ansible目录来定义它们。

![](Images/Day64_config4.png)

我们想要编辑的文件是hosts文件，使用文本编辑器我们可以进入并定义我们的主机。hosts文件包含大量关于如何使用和修改该文件的优秀说明。我们想要滚动到底部，我们将创建一个名为[windows]的新组，并将我们的`10.0.0.1` IP地址添加到该主机下。保存文件。

![](Images/Day64_config5.png)

然而，请记住我说过你需要启用SSH才能让Ansible连接到你的系统。正如你在下面看到的，当我运行`ansible windows -m ping`时，我们得到了一个unreachable（不可达），因为通过SSH连接失败了。

![](Images/Day64_config6.png)

我现在也开始向我们的清单中添加一些额外的主机，这个文件的另一个名称是库存（inventory），因为这里是你将要定义所有设备的地方，网络设备、交换机和路由器等也可以在这里添加并分组。在我们的hosts文件中，我还添加了我用于访问linux组系统的凭据。

![](Images/Day64_config7.png)

现在如果我们运行`ansible linux -m ping`，我们会得到如下所示的成功结果。

![](Images/Day64_config8.png)

然后我们有节点需求，这些是你希望在其上自动化配置的目标系统。我们在这些系统上不需要为Ansible安装任何东西（我的意思是我们可能会安装软件，但Ansible不需要客户端）。Ansible将通过SSH连接并通过SFTP发送任何内容。（如果你愿意，并且你已经配置了SSH，你可以使用SCP代替SFTP。）

### Ansible命令

你看到了我们能够对我们的Linux机器运行`ansible linux -m ping`并得到响应，基本上使用Ansible，我们有能力运行许多ad hoc（临时）命令。但显然，你可以针对一组系统运行此命令并获取信息。[ad hoc命令](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html)

如果你发现自己重复执行命令，或者更糟的是，你必须登录到各个系统来运行这些命令，那么Ansible可以在那里提供帮助。例如，下面的简单命令将为我们添加到linux组中的所有系统提供操作系统详细信息的输出。
`ansible linux -a "cat /etc/os-release"`

其他用例可能包括重启系统、复制文件、管理包和用户。你还可以将ad hoc命令与Ansible模块结合使用。

Ad hoc命令使用声明式模型，计算并执行达到指定最终状态所需的操作。它们通过在开始之前检查当前状态来实现一种幂等性（idempotence），除非当前状态与指定的最终状态不同，否则不做任何事。

## 相关资料

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)


[第65天](day65.md)见。


