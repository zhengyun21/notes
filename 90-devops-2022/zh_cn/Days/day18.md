---
title: '#90DaysOfDevOps - SSH 与 Web 服务器 - 第18天'
published: false
description: 90DaysOfDevOps - SSH 与 Web 服务器
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048733
---
## SSH 与 Web 服务器

正如我们在整个课程中提到的，你很可能会管理大量的远程 Linux 服务器，因此，你需要确保与这些远程服务器的连接是安全的。在本节中，我们想介绍一些每个人都应该知道的 SSH 基础知识，这将帮助你建立与远程系统的安全隧道。

- 使用 SSH 建立连接
- 传输文件
- 创建你的私钥

### SSH 简介

- Secure Shell（安全外壳协议）
- 网络协议（Networking Protocol）
- 允许安全通信
- 可以保护任何网络服务
- 通常用于远程命令行访问

在我们的环境中，如果你一直在跟随学习，我们已经使用了 SSH，但这一切都是在我们 Vagrant 配置中配置和自动化的，所以我们只需要运行 `vagrant ssh` 就能访问我们的远程虚拟机。

如果我们的远程机器不在与我们的工作站同一系统上，而是在一个远程位置，也许是一个云端系统或运行在只能通过互联网访问的数据中心中，我们就需要一种安全的方式来访问和管理该系统。

SSH 在客户端和服务器之间提供了一个安全隧道，这样恶意行为者就无法截获任何信息。

![](Images/Day18_Linux1.png)

服务器上有一个服务器端 SSH 服务始终在运行，并监听特定的 TCP 端口（22）。

如果我们使用正确的凭据或 SSH 密钥从客户端连接，那么我们就获得了对该服务器的访问权限。

### 为我们的系统添加桥接网络适配器

为了让我们在当前 VirtualBox 虚拟机中使用这个功能，我们需要为机器添加一个桥接网络适配器。

关闭你的虚拟机，在 VirtualBox 中右键点击你的机器并选择设置。在新窗口中选择网络。

![](Images/Day18_Linux2.png)

现在重新启动你的机器，你现在会在你的本地机器上获得一个 IP 地址。你可以使用 `ip addr` 命令确认这一点。

### 确认 SSH 服务器正在运行

我们知道我们的机器上已经配置了 SSH，因为我们一直在配合 Vagrant 使用它，但我们可以通过运行以下命令来确认：

`sudo systemctl status ssh`

![](Images/Day18_Linux3.png)

如果你的系统没有 SSH 服务器，你可以通过执行这个命令来安装它：`sudo apt install openssh-server`

然后你要确保如果防火墙正在运行，SSH 是被允许的。我们可以使用 `sudo ufw allow ssh` 来实现，这在我们的配置中不是必需的，因为我们已经在 Vagrant 的 provisioning 中自动完成了。

### 远程访问 - SSH 密码

现在我们的 SSH 服务器正在端口 22 上监听任何传入的连接请求，并且我们已经添加了桥接网络，我们可以使用本地机器上的 putty 或 SSH 客户端通过 SSH 连接到我们的系统。

![](Images/Day18_Linux4.png)

然后点击 open，如果这是你第一次通过这个 IP 地址连接到该系统，你会收到这个警告。我们知道这是我们的系统，所以你可以选择 yes。

![](Images/Day18_Linux5.png)

然后系统会提示我们输入用户名（vagrant）和密码（默认密码 - vagrant）。在下面你会看到我们现在正在使用 SSH 客户端（Putty）通过用户名和密码连接到我们的机器。

![](Images/Day18_Linux6.png)

在这个阶段，我们已经从远程客户端连接到了我们的虚拟机，我们可以在系统上执行命令。

### 远程访问 - SSH 密钥

上述方法是访问系统的简单方式，但它仍然依赖于用户名和密码，如果某个恶意行为者获取了这些信息以及你系统的公共地址或 IP，那么系统很容易被入侵。这就是 SSH 密钥更受青睐的原因。

SSH 密钥意味着我们提供一对密钥，这样客户端和服务器都知道这是一个受信任的设备。

创建密钥很简单。在我们的本地机器（Windows）上，我们可以发出以下命令，事实上，如果你在安装了 ssh-client 的任何系统上，我相信这个相同的命令都会起作用？

`ssh-keygen -t ed25519`

我不会在这里深入讨论 `ed25519` 是什么以及它的含义，但如果你想了解更多关于密码学（cryptography）的知识，可以去搜索一下。

![](Images/Day18_Linux7.png)

此时，我们创建的 SSH 密钥存储在 `C:\Users\micha/.ssh/`

但为了将这个密钥与我们的 Linux 虚拟机关联起来，我们需要复制这个密钥。我们可以使用 `ssh-copy-id vagrant@192.168.169.135` 来实现。

我在 Windows 客户端上使用 Powershell 创建了我的密钥，但这里没有 `ssh-copy-id` 可用。在 Windows 上有多种方法可以实现这一点，在网上简单搜索一下就能找到替代方案，但我将直接在我的 Windows 机器上使用 git bash 来完成复制。

![](Images/Day18_Linux8.png)

现在我们可以回到 Powershell 测试我们的连接现在是否可以使用 SSH 密钥工作，并且不需要密码。

`ssh vagrant@192.168.169.135`

![](Images/Day18_Linux9.png)

如果需要，我们可以使用密码短语（passphrase）进一步提高安全性。我们还可以更进一步，完全禁用密码，意味着只允许通过 SSH 密钥对进行连接。你可以在以下配置文件中实现这一点。

`sudo nano /etc/ssh/sshd_config`

这里有一行写着 `PasswordAuthentication yes`，这一行会被 `#` 注释掉，你应该取消注释并将 yes 改为 no。然后你需要使用 `sudo systemctl reload sshd` 重新加载 SSH 服务。

## 设置 Web 服务器

这与我们上面刚做的 SSH 内容没有直接关联，但我想把它包含进来，因为这也是另一个你可能觉得有点可怕但实际上不应该的任务。

我们有我们的 Linux 实验虚拟机，在这个阶段，我们想向我们的虚拟机添加一个 Apache web 服务器，这样我们就可以从它托管一个简单的网站，向我的家庭网络提供服务。请注意，这个网页不会从互联网访问，这可以做到，但这里不会介绍。

你可能还会看到它被称为 LAMP 栈。

- **L**inux 操作系统
- **A**pache Web 服务器
- **m**ySQL 数据库
- **P**HP

### Apache2
Apache2 是一个开源的 HTTP 服务器。我们可以使用以下命令安装 apache2。

`sudo apt-get install apache2`

要确认 apache2 已正确安装，我们可以运行 `sudo service apache2 restart`

然后使用 SSH 教程中的桥接网络地址打开浏览器并访问该地址。我的是 `http://192.168.169.135/`

![](Images/Day18_Linux10.png)

### mySQL
MySQL 是一个数据库，我们将用它来存储我们简单网站的数据。要安装 MySQL，我们应该使用以下命令 `sudo apt-get install mysql-server`

### PHP
PHP 是一种服务器端脚本语言，我们将用它来与 MySQL 数据库交互。最后的安装是使用 `sudo apt-get install php libapache2-mod-php php-mysql` 来安装 PHP 及其依赖项。

我们想要做的第一个配置更改是：开箱即用的 apache 使用的是 index.html，我们希望它改用 index.php。

我们将使用 `sudo nano /etc/apache2/mods-enabled/dir.conf`，并将 index.php 移到列表的第一位。

![](Images/Day18_Linux11.png)

重启 apache2 服务 `sudo systemctl restart apache2`

现在让我们确认我们的系统已正确配置 PHP。使用以下命令创建以下文件，这将在 nano 中打开一个空白文件。

`sudo nano /var/www/html/90Days.php`

然后复制以下内容并使用 control + x 退出并保存你的文件。

```
<?php
phpinfo();
?>
```

现在再次导航到你的 Linux 虚拟机 IP，并在 URL 末尾加上 90Days.php。`http://192.168.169.135/90Days.php` 如果 PHP 配置正确，你应该会看到类似下面的内容。

![](Images/Day18_Linux12.png)

### WordPress 安装

然后我按照这个教程在我们的 LAMP 栈上安装 WordPress，下面显示了一些命令，如果教程中没有正确显示的话。[How to install wordpress on Ubuntu with LAMP](https://blog.ssdnodes.com/blog/how-to-install-wordpress-on-ubuntu-18-04-with-lamp-tutorial/)


`sudo mysql -u root -p`

`CREATE DATABASE wordpressdb;`

`CREATE USER 'admin-user'@'localhost' IDENTIFIED BY 'password';`

`GRANT ALL PRIVILEGES ON wordpressdb.* TO 'admin-user'@'localhost';`

`FLUSH PRIVILEGES;`

`EXIT;`

`sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip`

`sudo systemctl restart apache2`

`cd /var/www`

`sudo curl -O https://wordpress.org/latest.tar.gz`

`sudo tar -xvf latest.tar.gz`

`sudo rm latest.tar.gz`

此时你处于链接文章中的第4步，你需要按照步骤确保 WordPress 目录的所有权限都正确设置。

因为这是内部使用的，所以你不需要在这一步中"生成安全密钥"。跳到第5步，更改 Apache 配置以指向 WordPress。

然后，如果所有配置都正确，你将能够通过内部网络地址访问并运行 WordPress 安装。

## 资源

- [Client SSH GUI - Remmina](https://remmina.org/)
- [The Beginner's guide to SSH](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

[第19天](day19.md)见。
