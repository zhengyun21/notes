---
title: '#90DaysOfDevOps - Ansible Playbooks（剧本） - 第65天'
published: false
description: 90DaysOfDevOps - Ansible Playbooks（剧本）
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049054
---
### Ansible Playbooks（Ansible 剧本）

在本节中，我们将探讨我认为至少是 Ansible 的主要用途。当然，能够执行单个命令并对多台不同的服务器执行简单的命令（例如重启一长串服务器），从而省去逐一连接每台服务器的麻烦，这非常棒。

但是，如果我们要对一个裸操作系统进行声明式管理，指定我们希望在该系统上运行的软件和服务，并确保它们都处于期望的运行状态，那又该怎么做呢？

这就是 Ansible Playbooks（Ansible 剧本）发挥作用的地方。Playbook（剧本）使我们能够对一组服务器执行配置和安装任务。

### Playbook（剧本）格式

Playbook > Plays（剧目） > Tasks（任务）

对于任何有体育背景的人来说，你可能已经接触过"战术手册"（playbook）这个术语。战术手册告诉团队如何比赛，由各种 plays（战术/剧目）和 tasks（任务）组成。如果我们把 plays 看作体育或比赛中的固定套路，而 tasks 则与每个 play 相关联——一个 play 可以包含多个 tasks，而在一个 playbook 中，你可以有多个不同的 plays。

这些 playbook 是用 YAML（YAML ain't markup language，YAML 不是一种标记语言）编写的。你会发现，到目前为止我们涵盖的许多章节，特别是容器和 Kubernetes，都使用了 YAML 格式的配置文件。

让我们来看一个简单的 playbook，名为 playbook.yml。

```
- name: Simple Play
  hosts: localhost
  connection: local
  tasks:
    - name: Ping me
      ping:
    - name: print os
      debug:
        msg: "{{ ansible_os_family }}"
```

你可以在 [simple_play](days/../Configmgmt/simple_play.yml) 找到上述文件。如果我们然后使用 `ansible-playbook simple_play.yml` 命令，我们将经历以下步骤。

![](Images/Day65_config1.png)

你可以看到第一个任务 "gathering steps"（收集事实）已经执行，但我们并没有触发或要求这个操作？这个模块由 playbook 自动调用，用于收集关于远程主机的有用变量。[ansible.builtin.setup](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/setup_module.html)

我们的第二个任务是设置一个 ping，这不是 ICMP ping，而是一个 Python 脚本，用于在成功连接到远程主机或本地主机时返回 `pong`。[ansible.builtin.ping](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/ping_module.html)

然后是我们的第三个任务——实际上是我们定义的第二个任务，因为第一个任务默认会运行，除非你去禁用它——这个任务是打印一条消息，告诉我们操作系统信息。在这个任务中，我们使用了条件判断。我们可以针对各种不同类型的操作系统运行这个 playbook，它会返回操作系统的名称。我们这里只是简单地输出这个消息以便查看，但我们可以添加一个类似这样的任务：

```
tasks:
  - name: "shut down Debian flavoured systems"
    command: /sbin/shutdown -t now
    when: ansible_os_family == "Debian"
```

### 使用 Vagrant 搭建我们的环境

我们将使用 Vagrant 来搭建我们的节点环境。我将保持在一个合理的 4 个节点，但希望你能看到，这完全可以轻松扩展到 300 或 3000 个节点，这就是 Ansible 和其他配置管理工具的强大之处，能够配置你的服务器。

你可以在这里找到这个文件（[Vagrantfile](Configmgmt/Vagrantfile)）

```
Vagrant.configure("2") do |config|
  servers=[
    {
      :hostname => "db01",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.130",
      :ssh_port => '2210'
    },
    {
      :hostname => "web01",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.131",
      :ssh_port => '2211'
    },
    {
      :hostname => "web02",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.132",
      :ssh_port => '2212'
    },
    {
      :hostname => "loadbalancer",
      :box => "bento/ubuntu-21.10",
      :ip => "192.168.169.134",
      :ssh_port => '2213'
    }

  ]

config.vm.base_address = 600

  servers.each do |machine|

    config.vm.define machine[:hostname] do |node|
      node.vm.box = machine[:box]
      node.vm.hostname = machine[:hostname]

      node.vm.network :public_network, bridge: "Intel(R) Ethernet Connection (7) I219-V", ip: machine[:ip]
      node.vm.network "forwarded_port", guest: 22, host: machine[:ssh_port], id: "ssh"

      node.vm.provider :virtualbox do |v|
        v.customize ["modifyvm", :id, "--memory", 2048]
        v.customize ["modifyvm", :id, "--name", machine[:hostname]]
      end
    end
  end

end
```

使用 `vagrant up` 命令在 VirtualBox 中启动这些机器。你可能可以分配更多内存，也可能想为每台机器定义不同的 private_network 地址，但这在我的环境中是可行的。记住，我们的控制机是我们在 Linux 章节中部署的 Ubuntu 桌面系统。

如果你的资源有限，那么你也可以运行 `vagrant up web01 web02` 来只启动我们这里要使用的 Web 服务器。

### Ansible 主机配置

现在我们的环境已经准备好了，我们可以检查 ansible，为此我们将使用我们的 Ubuntu 桌面（你也可以使用其他方法，但同样可以使用网络上任何可访问以下网络的 Linux 机器）作为我们的控制机。让我们也将新节点添加到 ansible hosts（主机）文件中的组里，你可以把这个文件看作一个清单（inventory）。另一种方法是使用另一个清单文件，在你的 ansible 命令中通过 `-i filename` 来调用，这可能比使用 hosts 文件更有用，因为你可以为不同的环境使用不同的文件，比如生产环境、测试环境和预发布环境。因为我们使用的是默认的 hosts 文件，所以我们不需要指定，因为这将是默认使用的文件。

我已将以下内容添加到默认的 hosts 文件中。

```
[control]
ansible-control

[proxy]
loadbalancer

[webservers]
web01
web02

[database]
db01

```
![](Images/Day65_config2.png)

在继续之前，我们想确保我们可以对节点运行命令。让我们运行 `ansible nodes -m command -a hostname`，这个简单的命令将测试我们的连接性并返回主机名。

另请注意，我已将这些节点和 IP 添加到我的 Ubuntu 控制节点的 /etc/hosts 文件中，以确保连接性。我们可能还需要从 Ubuntu 机器对每个节点进行 SSH 配置。

```
192.168.169.140 ansible-control
192.168.169.130 db01
192.168.169.131 web01
192.168.169.132 web02
192.168.169.133 loadbalancer
```
![](Images/Day65_config3.png)

在这个阶段，我们需要完成控制机和服务器节点之间的 SSH 密钥设置。这就是我们接下来要做的。另一种方法是在 hosts 文件中添加变量来提供用户名和密码。我不建议这样做，因为这永远不会是最佳实践。

要在节点之间设置 SSH 并共享密钥，请按照以下步骤操作。系统会提示你输入密码（`vagrant`），你可能需要多次按 `y` 来确认接受。

`ssh-keygen`

![](Images/Day65_config5.png)

`ssh-copy-id localhost`

![](Images/Day65_config6.png)

现在，如果你所有的虚拟机都已开启，那么你可以运行 `ssh-copy-id web01 && ssh-copy-id web02 && ssh-copy-id loadbalancer && ssh-copy-id db01`，这将提示你输入密码，在我们的案例中密码是 `vagrant`。

我没有运行所有的虚拟机，只运行了 Web 服务器，所以我执行了 `ssh-copy-id web01 && ssh-copy-id web02`

![](Images/Day65_config7.png)

在运行任何 playbook 之前，我喜欢确保与我的组有简单的连接，所以我运行了 `ansible webservers -m ping` 来测试连接性。

![](Images/Day65_config4.png)


### 我们的第一个"真正的"Ansible Playbook（Ansible 剧本）
我们的第一个 Ansible playbook 将要配置我们的 Web 服务器，我们在 hosts 文件中将这些服务器归在 [webservers] 组下。

在运行我们的 playbook 之前，我们可以确认 web01 和 web02 上没有安装 apache。下面的截图顶部显示了我为运行此 playbook 在 ansible 控制机中创建的文件夹和文件布局，我们有 `playbook1.yml`，然后在 templates 文件夹中有 `index.html.j2` 和 `ports.conf.j2` 文件。你可以在仓库中上面列出的文件夹中找到这些文件。

然后我们通过 SSH 进入 web01 检查是否安装了 apache。

![](Images/Day65_config8.png)

从上面可以看出，我们的 web01 上还没有安装 apache，所以我们可以通过运行下面的 playbook 来解决这个问题。


```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps"
  tasks:
  - name: ensure apache is at the latest version
    apt:
      name: apache2
      state: latest

  - name: write the apache2 ports.conf config file
    template:
      src: templates/ports.conf.j2
      dest: /etc/apache2/ports.conf
    notify:
    - restart apache

  - name: write a basic index.html file
    template:
      src: templates/index.html.j2
      dest: /var/www/html/index.html
    notify:
    - restart apache

  - name: ensure apache is running
    service:
      name: apache2
      state: started

  handlers:
    - name: restart apache
      service:
        name: apache2
        state: restarted
```
分解上面的 playbook：

- `- hosts: webservers` 这表示我们要在这个名为 webservers 的组上运行此 playbook
- `become: yes` 意味着运行 playbook 的用户将在远程系统上成为 root。系统会提示你输入 root 密码。
- 然后我们有 `vars`，这定义了我们希望在整个 Web 服务器中使用的一些环境变量。

接下来我们开始我们的 tasks（任务）：

- 任务 1：确保 apache 运行最新版本
- 任务 2：从 templates 文件夹中的源文件写入 ports.conf 文件
- 任务 3：创建一个基本的 index.html 文件
- 任务 4：确保 apache 正在运行

最后我们有一个 handlers（处理程序）部分，[Handlers: Running operations on change](https://docs.ansible.com/ansible/latest/user_guide/playbooks_handlers.html)

"有时你希望一个任务只在机器上发生更改时才运行。例如，你可能希望在一个任务更新了某个服务的配置时重启该服务，但如果配置没有更改则不重启。Ansible 使用 handlers 来解决这个用例。Handlers 是只在收到通知时才运行的任务。每个 handler 应该有一个全局唯一的名称。"

在这个阶段，你可能会想：但我们部署了 5 台虚拟机（包括我们的 Ubuntu 桌面机，它充当我们的 Ansible 控制机）。其他系统将在本节的其余部分中发挥作用。

### 运行我们的 Playbook（剧本）

我们现在准备在我们的节点上运行我们的 playbook。要运行我们的 playbook，我们可以使用 `ansible-playbook playbook1.yml`。我们已经在 playbook 中定义了要运行的 hosts（主机），这将遍历我们定义的 tasks（任务）。

当命令完成时，我们会得到一个显示我们的 plays 和 tasks 的输出，这可能需要一些时间。你可以从下面的图片中看到，安装我们的期望状态花了一段时间。

![](Images/Day65_config9.png)

然后我们可以通过跳转到节点并检查节点上是否安装了软件来再次确认这一点。

![](Images/Day65_config10.png)

为了完善这个示例，由于我们使用上述配置部署了两台独立的 Web 服务器，我们现在可以导航到我们定义的相应 IP 地址并访问我们的新网站。

![](Images/Day65_config11.png)

随着我们在本节其余部分的深入，我们将在这个 playbook 的基础上继续构建。我也很有兴趣看看我们是否可以使用 Ansible 来引导我们的应用程序和配置，所以我们可能也会涉及这一点。你看到了我们可以在命令中使用 localhost，我们也可以针对 localhost 运行 playbook。

另外要补充的一点是，我们目前实际上只使用 Ubuntu 虚拟机，但 Ansible 对目标系统是不可知的（agnostic）。我们之前提到的管理系统的替代方案可能是逐台服务器管理（当你有大量服务器时，这是不可扩展的，即使有 3 个节点也会很痛苦），或者我们也可以使用 shell 脚本——我们在 Linux 章节中介绍过——但这些节点可能不同，所以虽然可以做到，但之后需要有人维护和管理这些脚本。Ansible 是免费的，并且按下简单按钮就能实现，而不必拥有专门的脚本。

## 相关资源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最后一个播放列表是本节大部分代码和想法的来源，是一个非常棒的资源，以视频格式进行讲解。

[第66天](day66.md)见。
