---
title: '#90DaysOfDevOps - Ansible Playbooks（剧本）续篇... - 第66天'
published: false
description: 90DaysOfDevOps - Ansible Playbooks（剧本）续篇...
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048712
---
## Ansible Playbooks（Ansible 剧本）续篇...

在上一个章节中，我们开始使用 Vagrantfile 创建我们的小型实验室，部署了 4 台机器，并使用我们在 Linux 章节中创建的 Linux 机器作为我们的 ansible 控制系统。

我们还运行了几个 playbook 场景，最后我们得到了一个 playbook，使 web01 和 web02 成为了独立的 Web 服务器。

![](Images/Day66_config1.png)

### 保持整洁

在我们进入进一步的自动化和部署之前，我们应该介绍一下如何保持我们的 playbook 精简整洁，以及如何将我们的 tasks（任务）和 handlers（处理程序）分离到子文件夹中。

我们基本上要将我们的 tasks 复制到各自文件夹中的独立文件里。

```
- name: ensure apache is at the latest version
  apt: name=apache2 state=latest

- name: write the apache2 ports.conf config file
  template:
    src=templates/ports.conf.j2
    dest=/etc/apache2/ports.conf
  notify: restart apache

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
```

对 handlers 也做同样的处理。

```
- name: restart apache
  service:
    name: apache2
    state: restarted
```

然后在我们现在命名为 `playbook2.yml` 的 playbook 中，我们指向这些文件。所有文件都可以在 [ansible-scenario2](Days/../Configmgmt/ansible-scenario2/) 找到。

你可以在你的控制机上测试这个。如果你已经从仓库复制了文件，你应该会注意到 "write a basic index.html file" 中有些变化。

![](Images/Day66_config2.png)

让我们看看我做了哪些简单的改动。使用 `curl web01:8000`

![](Images/Day66_config3.png)

我们刚刚整理了我们的 playbook，并开始分离那些在大规模使用时可能会让 playbook 变得非常难以管理的部分。

### Roles（角色）和 Ansible Galaxy

目前我们已经部署了 4 台虚拟机，并将其中 2 台配置为我们的 Web 服务器，但我们还有一些更具体的功能，即数据库服务器和负载均衡器（loadbalancer）或代理（proxy）。为了做到这一点并整理我们的仓库，我们可以在 Ansible 中使用 roles（角色）。

为此，我们将使用 `ansible-galaxy` 命令，它用于在共享仓库中管理 ansible roles（角色）。

![](Images/Day66_config4.png)

我们将使用 `ansible-galaxy` 为 apache2 创建一个 role（角色），我们将把 Web 服务器的具体配置放在这个 role 中。

![](Images/Day66_config5.png)

上面的命令 `ansible-galaxy init roles/apache2` 将创建我们在上面展示的文件夹结构。我们的下一步是将现有的 tasks 和 templates 移动到新的结构中的相关文件夹。

![](Images/Day66_config6.png)

复制粘贴可以轻松移动这些文件，但我们还需要修改 tasks/main.yml，使其指向 apache2_install.yml。

我们还需要修改我们的 playbook，以引用我们的新 role。在 playbook1.yml 和 playbook2.yml 中，我们以不同的方式定义了我们的 tasks 和 handlers，因为我们在两个版本之间做了更改。我们需要将我们的 playbook 修改为按如下方式使用这个 role：

```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - apache2
```

![](Images/Day66_config7.png)

我们现在可以再次运行我们的 playbook，这次使用新的 playbook 名称 `ansible-playbook playbook3.yml`，你会注意到有弃用警告，我们接下来可以修复它。

![](Images/Day66_config8.png)

好的，虽然有弃用警告，但我们的 playbook 运行成功了，我们现在应该修正我们的做法。为此，我已将 tasks/main.yml 中的 include 选项更改为 import_tasks，如下所示。

![](Images/Day66_config9.png)

你可以在 [ansible-scenario3](Days/Configmgmt/ansible-scenario3) 中找到这些文件。

我们还将在使用 `ansible-galaxy` 的同时创建更多的 roles，我们将创建：

- common = 用于我们所有的服务器（`ansible-galaxy init roles/common`）
- nginx = 用于我们的负载均衡器（`ansible-galaxy init roles/nginx`）

![](Images/Day66_config10.png)

我将在这里结束，在下一节中，我们将开始处理那些我们已经部署但尚未配置的其他节点。

## 相关资源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最后一个播放列表是本节大部分代码和想法的来源，是一个非常棒的资源，以视频格式进行讲解。

[第67天](day67.md)见。
