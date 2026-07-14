---
title: '#90DaysOfDevOps - 使用 Roles（角色）和部署负载均衡器 - 第67天'
published: false
description: 90DaysOfDevOps - 使用 Roles（角色）和部署负载均衡器
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048713
---
## 使用 Roles（角色）和部署负载均衡器

在上一节中，我们介绍了 roles（角色），并使用 `ansible-galaxy` 命令来帮助我们创建将要使用的一些 roles 的文件夹结构。我们最终得到了一个更加整洁的配置代码工作仓库，因为所有内容都被隐藏在我们的 role 文件夹中。

然而，我们只使用了 apache2 role，并且有一个可用的 playbook3.yaml 来处理我们的 Web 服务器。

如果你之前只使用了 `vagrant up web01 web02`，那么现在是时候运行 `vagrant up loadbalancer` 了，这将启动另一个 Ubuntu 系统，我们将用它作为负载均衡器/代理（Load Balancer/Proxy）。

我们已经在 hosts 文件中定义了这台新机器，但在它可用之前我们还没有配置 SSH 密钥，所以当系统启动并准备好后，我们还需要运行 `ssh-copy-id loadbalancer`。

### Common（通用）role

我在昨天 session 的结束时创建了 `common` role。common 将在我们所有的服务器上使用，而其他 roles 则用于特定的用例。现在，我作为 common 要安装的应用程序有些随意，我看不出有太多理由要这样做，但这展示了目标。在我们的 common role 文件夹结构中，导航到 tasks 文件夹，你会看到一个 main.yml。在这个 yaml 中，我们需要将它指向我们的 install_tools.yml 文件，我们通过添加一行 `- import_tasks: install_tools.yml` 来实现。这以前用的是 `include`，但它很快就会被弃用，所以我们使用 import_tasks。

```
- name: "Install Common packages"
  apt: name={{ item }} state=latest
  with_items:
   - neofetch
   - tree
   - figlet
```

然后我们在 playbook 中为每个 host（主机）块添加 common role。

```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - common
    - apache2
```

### nginx

下一步是在我们的负载均衡器虚拟机上安装和配置 nginx。与 common 文件夹结构类似，我们基于上一节创建了 nginx。

首先，我们要在我们的 playbook 中添加一个 host 块。这个块将包含我们的 common role，然后是新的 nginx role。

Playbook 可以在这里找到。[playbook4.yml](Days/../Configmgmt/ansible-scenario4/playbook4.yml)

```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - common
    - apache2

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
```

为了让这一切有意义，我们必须定义我们希望运行的 tasks，同样地，我们将修改 tasks 中的 main.yml 来指向两个文件，一个是安装文件，一个是配置文件。

我还根据我们期望的结果修改了一些其他文件，查看 [ansible-scenario4](Days/Configmgmt/ansible-scenario4) 文件夹中所有更改的文件。你应该检查 nginx 文件夹中的 tasks、handlers 和 templates 文件夹，你会发现那些额外的更改和文件。

### 运行更新后的 playbook

从昨天开始，我们添加了 common role，它现在将在我们的系统上安装一些软件包，然后我们还添加了 nginx role，它包括安装和配置。

让我们使用 `ansible-playbook playbook4.yml` 运行我们的 playbook4.yml。

![](Images/Day67_config1.png)

现在我们已经配置好了 Web 服务器和负载均衡器，我们应该能够访问 http://192.168.169.134/，这是我们负载均衡器的 IP 地址。

![](Images/Day67_config2.png)

如果你正在跟着做，但没有达到这个状态，那可能是由于你环境中的服务器 IP 地址不同。该文件可以在 `templates\mysite.j2` 中找到，看起来类似于下面这样：你需要用你的 Web 服务器 IP 地址进行更新。

```
    upstream webservers {
        server 192.168.169.131:8000;
        server 192.168.169.132:8000;
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```
我很有信心我们安装的一切都很好，但让我们使用 ansible 的 adhoc（临时）命令来检查这些常用工具的安装情况。

`ansible loadbalancer -m command -a neofetch`

![](Images/Day67_config3.png)

## 相关资源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最后一个播放列表是本节大部分代码和想法的来源，是一个非常棒的资源，以视频格式进行讲解。

[第68天](day68.md)见。
