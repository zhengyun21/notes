---
title: '#90DaysOfDevOps - 管理你的 Linux 系统、文件系统与存储 - 第16天'
published: false
description: '90DaysOfDevOps - 管理你的 Linux 系统、文件系统与存储'
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048702
---
## 管理你的 Linux 系统、文件系统与存储

到目前为止，我们已经简要概述了 Linux 和 DevOps，然后使用 Vagrant 设置了我们的实验环境（[第14天](day14.md)），接着我们接触了一小部分命令，这些命令将成为你在终端中日常使用的工具（[第15天](day15.md)）。

在这里，我们将研究管理 Linux 系统的三个关键领域：更新系统、安装软件、了解系统文件夹的用途，此外我们还将介绍存储相关内容。

## 管理 Ubuntu 和软件

我们要看的第一个内容是如何更新我们的操作系统。你们中的大多数人应该都熟悉在 Windows 和 macOS 上的这个过程，但在 Linux 桌面和服务器上看起来略有不同。

我们将研究 apt 包管理器（package manager），这是我们将在 Ubuntu 虚拟机上用于更新和软件安装的工具。

一般来说，至少在开发工作站上，我会在安装任何软件之前运行这个命令，以确保我从中央仓库获取最新的可用更新。

`sudo apt-get update`

![](Images/Day16_Linux1.png)

现在我们已经更新了 Ubuntu 虚拟机并安装了最新的操作系统更新。我们现在想在这里安装一些软件。

让我们选择 `figlet`，这是一个生成文本横幅的程序。

如果我们在终端中输入 `figlet`，你会看到我们的系统上并没有安装它。

![](Images/Day16_Linux2.png)

从上面的输出中你会看到，它确实给了我们一些 `apt` 安装选项可以尝试。这是因为在默认仓库中有一个名为 figlet 的程序。让我们尝试 `sudo apt install figlet`

![](Images/Day16_Linux3.png)

我们现在可以使用我们的 `figlet` 应用了，如下所示。

![](Images/Day16_Linux4.png)

如果我们想要移除它或任何已安装的软件，我们也可以通过 `apt` 包管理器来完成。

`sudo apt remove figlet`

![](Images/Day16_Linux5.png)

我们还可以向系统添加第三方仓库，开箱即用我们只能访问 Ubuntu 的默认仓库。

例如，如果我们想在 Ubuntu 虚拟机上安装 Vagrant，我们现在还做不到，你可以在下面第一个执行的命令中看到。然后我们添加信任 HashiCorp 仓库的密钥，再将该仓库添加到我们的系统中。

![](Images/Day16_Linux6.png)

一旦我们添加了 HashiCorp 仓库，我们就可以运行 `sudo apt install vagrant` 并在我们的系统上安装 Vagrant。

![](Images/Day16_Linux7.png)

在软件安装方面有很多选择，不同的包管理器选项，在 Ubuntu 中我们还可以使用 snaps 来安装软件。

希望这能让你了解如何在 Linux 上管理操作系统和软件安装。

## 文件系统详解

Linux 由配置文件组成，如果你想更改任何内容，就要修改这些配置文件。

在 Windows 上，你有 C: 盘，这就是我们所说的根目录。在 Linux 上，我们有 `/`，在这里我们会找到 Linux 系统上的重要文件夹。

![](Images/Day16_Linux8.png)

- `/bin` - 是 binary（二进制）的缩写，bin 文件夹是你的系统所需的二进制文件、可执行文件和工具大多存放的地方。

![](Images/Day16_Linux9.png)

- `/boot` - 系统启动所需的所有文件。如何启动，以及从哪个驱动器启动。

![](Images/Day16_Linux10.png)

- `/dev` - 你可以在这里找到设备信息，这里会有指向你的磁盘驱动器的指针，`sda` 将是你的主操作系统磁盘。

![](Images/Day16_Linux11.png)

- `/etc` - 这可能是你 Linux 系统上最重要的文件夹，这里存放着大部分的配置文件。

![](Images/Day16_Linux12.png)

- `/home` - 这里存放着用户文件夹和文件。我们有我们的 vagrant 用户文件夹。在这里你会找到我们在命令部分使用过的 `Documents` 和 `Desktop` 文件夹。

![](Images/Day16_Linux13.png)

- `/lib` - 我们提到 `/bin` 是我们的二进制文件和可执行文件存放的地方，`/lib` 则是这些文件所需的共享库（shared libraries）存放的地方。

![](Images/Day16_Linux14.png)

- `/media` - 这里存放的是可移动设备。

![](Images/Day16_Linux15.png)

- `/mnt` - 这是一个临时挂载点（temporary mount point）。我们将在下一节的存储部分详细介绍。

![](Images/Day16_Linux16.png)

- `/opt` - 可选软件包（optional software packages）。你会注意到这里存放了一些 Vagrant 和 VirtualBox 软件。

![](Images/Day16_Linux17.png)

- `/proc` - 内核（kernel）和进程信息，类似于 `/dev`

![](Images/Day16_Linux18.png)

- `/root` - 要访问这个文件夹你需要使用 sudo。这是 root 用户的主文件夹。

![](Images/Day16_Linux19.png)

- `/run` - 应用程序状态的占位符（placeholder）。

![](Images/Day16_Linux20.png)

- `/sbin` - Sudo bin，类似于 bin 文件夹，但这些工具是用于需要提升的超级用户权限的。

![](Images/Day16_Linux21.png)

- `/tmp` - 临时文件。

![](Images/Day16_Linux22.png)

- `/usr` - 如果我们作为普通用户安装了软件包，通常会安装在 `/usr/bin` 位置。

![](Images/Day16_Linux23.png)

- `/var` - 我们的应用程序安装在 `bin` 文件夹中。我们需要一个地方来存储所有的日志文件，这就是 `/var`

![](Images/Day16_Linux24.png)

## 存储

当我们来到 Linux 系统或任何系统时，我们可能想了解可用的磁盘以及这些磁盘上有多少可用空间。接下来的几个命令将帮助我们识别、使用和管理存储。

- `lsblk` 列出块设备（List Block devices）。`sda` 是我们的物理磁盘，而 `sda1, sda2, sda3` 是该磁盘上的分区。

![](Images/Day16_Linux25.png)

- `df` 给我们提供了关于这些分区的更多详细信息，包括总量、已用量和可用量。你可以在这里解析其他标志，我通常使用 `df -h` 来以人类可读的格式输出数据。

![](Images/Day16_Linux26.png)

如果你在系统中添加了一块新磁盘（这在 Windows 中也是一样的），你需要在磁盘管理中格式化磁盘，在 Linux 终端中你可以使用 `sudo mkfs -t ext4 /dev/sdb` 来完成，其中 sdb 对应我们新添加的磁盘。

然后我们需要挂载我们新格式化的磁盘，使其可用。我们会在之前提到的 `/mnt` 文件夹中进行，使用 `sudo mkdir NewDisk` 在那里创建一个目录，然后使用 `sudo mount /dev/sdb newdisk` 将磁盘挂载到该位置。

也可能存在你需要安全地从系统中卸载存储设备，而不是直接从配置中拔出的情况。我们可以使用 `sudo umount /dev/sdb` 来完成。

如果你不想卸载那块磁盘，并且你打算将它用于数据库或其他持久化用例，那么你希望它在系统重启时仍然可用。为此，我们需要将该磁盘添加到 `/etc/fstab` 配置文件中以使其持久化，如果不这样做，机器重启后它将不可用，你必须手动执行上述过程。数据仍然会保留在磁盘上，除非你添加配置到这个文件中，否则它不会自动挂载。

一旦你编辑了 `fstab` 配置文件，你可以使用 `sudo mount -a` 检查你的配置是否正确，如果没有错误，那么你的更改现在将在重启后持久生效。

我们将在未来的课程中介绍如何使用文本编辑器编辑文件。

## 资源

- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

[第17天](day17.md)见
