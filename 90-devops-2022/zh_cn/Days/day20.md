---
title: '#90DaysOfDevOps - 开发工作站配置 - 美化一切 - 第20天'
published: false
description: 90DaysOfDevOps - 开发工作站配置 - 美化一切
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048734
---
## 开发工作站配置 - 美化一切

不要与我们配置 Linux 服务器的方式混淆，但我想展示一下我们在 Linux 桌面中拥有的选择和灵活性。

我已经使用 Linux 桌面将近一年了，我从外观和感觉的角度按照我想要的方式配置了它。使用我们在 Virtual Box 上的 Ubuntu VM，我们可以浏览一些我对我日常使用机器所做的自定义设置。

我制作了一个 YouTube 视频来展示其余的内容，因为有些人可能能够更好地跟着视频学习：

[![点击访问 YouTube 视频](Images/Day20_YouTube.png)](https://youtu.be/jeEslAtHfKc)

开箱即用，我们的系统看起来会像下面这样：

![](Images/Day20_Linux1.png)

我们还可以在下面看到我们默认的 bash shell，

![](Images/Day20_Linux2.png)

很多这些都取决于 dotfiles，这是我们在本系列最后一个 Linux 会话中要涵盖的内容。

### dotfiles

首先我想深入了解 dotfiles，我在前面某一天说过，Linux 是由配置文件组成的。这些 dotfiles 是你的 Linux 系统和应用程序的配置文件。

我还要补充一点，dotfiles 不仅仅是用来定制和美化桌面的，还有一些 dotfile 的更改和配置可以帮助你提高工作效率。

正如我提到的，许多软件程序将它们的配置存储在这些 dotfiles 中。这些 dotfiles 协助管理功能。

每个 dotfile 都以 `.` 开头。你大概能猜到命名是怎么来的吧？

到目前为止，我们一直在使用 bash 作为我们的 shell，这意味着在我们的主文件夹中会有 .bashrc 和 .bash_profile。你可以在下面看到我们系统上的一些 dotfiles。

![](Images/Day20_Linux3.png)

我们将要更换我们的 shell，所以稍后我们会看到一个新的 `.zshrc` 配置 dotfile。

但现在你知道了，如果我们提到 dotfiles，你就知道它们是配置文件。我们可以用它们来为我们的命令提示符添加别名，以及添加不同位置的路径。有些人会发布他们的 dotfiles，使其公开可用。你可以在我的 GitHub [MichaelCade/dotfiles](https://github.com/MichaelCade/dotfiles) 上找到我的，这里有我的自定义 `.zshrc` 文件，我选择的终端是 terminator，它在文件夹中也有一些配置文件，然后还有一些背景选项。

### ZSH

正如我在我们之前的交流中提到的，我们一直在使用 Ubuntu 自带的默认 shell bash。ZSH 非常相似，但它确实有一些优于 bash 的功能。

Zsh 具有交互式 Tab 补全、自动文件搜索、正则表达式集成、用于定义命令作用域的高级简写以及丰富的主题引擎等功能。

我们可以使用 `apt` 包管理器在我们的系统上安装 zsh。让我们从 bash 终端运行 `sudo apt install zsh`。我将从 VM 控制台而不是通过 SSH 连接来执行此操作。

当安装命令完成后，你可以在终端中运行 `zsh`，这将启动一个 shell 配置脚本。

![](Images/Day20_Linux4.png)

我对上面的问题选择了 `1`，现在我们有更多选项了。

![](Images/Day20_Linux5.png)

你可以从这个菜单中看到，我们可以进行一些开箱即用的编辑，以根据我们的需求配置 ZSH。

如果你用 `0` 退出向导，然后使用 `ls -al | grep .zshrc`，你应该会看到我们有一个新的配置文件。

现在我们希望每次打开终端时都让 zsh 成为我们的默认 shell，我们可以通过运行以下命令来更改我们的 shell：`chsh -s $(which zsh)`，然后我们需要注销并重新登录才能使更改生效。

当你重新登录并打开终端时，它应该看起来像这样。我们还可以通过运行 `which $SHELL` 来确认我们的 shell 现在已经切换过来了。

![](Images/Day20_Linux6.png)

我通常在启动的每个 Ubuntu 桌面上都会执行此步骤，并且一般来说，即使不进一步配置，我也发现 zsh shell 比 bash 稍微快一点。

### OhMyZSH

接下来我们想让终端看起来更好一些，并添加一些功能来帮助我们在终端中移动。

OhMyZSH 是一个免费开源的框架，用于管理你的 zsh 配置。有很多插件、主题和其他东西，让与 zsh shell 的交互变得更加愉快。

你可以在 [ohmyzsh](https://ohmyz.sh/) 上了解更多信息。

让我们安装 Oh My ZSH，我们有几个选项：`curl`、`wget` 或 `fetch`，我们的系统上有前两个可用，但我将使用 `curl`。

`sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

当你运行上面的命令后，你应该会看到类似下面的输出。

![](Images/Day20_Linux7.png)

现在我们可以继续开始为我们的体验设置一个主题，Oh My ZSH 内置了超过 100 个主题，但我对所有应用程序和一切的首选都是 dracula 主题。

我还要补充一点，这两个插件在使用 Oh My ZSH 时是必须的。

`git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions`

`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting`

`nano ~/.zshrc`

编辑插件以现在包含 `plugins=(git zsh-autosuggestions zsh-syntax-highlighting)`。

## Gnome 扩展

我还使用 Gnome 扩展，特别是下面列出的这些。

[Gnome 扩展](https://extensions.gnome.org)

    - Caffeine 
    - CPU Power Manager
    - Dash to Dock 
    - Desktop Icons 
    - User Themes 

## 软件安装

我使用 `apt` 在机器上安装的程序的简短列表：

    - VSCode 
    - azure-cli 
    - containerd.io
    - docker
    - docker-ce 
    - google-cloud-sdk 
    - insomnia 
    - packer
    - terminator
    - terraform 
    - vagrant

### Dracula 主题

这个网站是我目前唯一使用的主题。看起来清晰、干净，一切都很棒。[Dracula Theme](https://draculatheme.com/) 当你有很多其他在机器上使用的程序时，它也能覆盖到。

从上面的链接中，我们可以在网站上搜索 zsh，你会发现至少有两个选项。

按照列出的说明手动安装或使用 git 安装。然后你需要最后按照下面的方式编辑你的 `.zshrc` 配置文件。

![](Images/Day20_Linux8.png)

接下来你会想要 [Gnome Terminal Dracula 主题](https://draculatheme.com/gnome-terminal)，所有说明也可以在这里找到。

实际上，要记录每一个步骤会花费我很长时间，所以我创建了一个视频来展示整个过程。（**点击下面的图片**）

[![](Images/Day20_YouTube.png)](https://youtu.be/jeEslAtHfKc)

如果你读到了这里，那么我们现在已经完成了 #90DaysOfDevOps 的 Linux 章节。我再次欢迎反馈和补充资源。

我也认为在这方面，通过视频向你展示很多步骤比把它们写在这里更容易，你觉得呢？我的目标是尽可能地回顾这些天，创建视频教程并添加进来，也许能更好地解释和展示我们涵盖的一些内容。你觉得怎么样？

## 相关资料

- [100秒了解 Bash](https://www.youtube.com/watch?v=I4EWvMFj37g)
- [Bash 脚本与实用示例 - 完整课程](https://www.youtube.com/watch?v=TPRSJbtfK4M)
- [SSH 图形客户端 - Remmina](https://remmina.org/)
- [SSH 初学者指南](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [100秒了解 Vim](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim 教程](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [学习 Linux 基础 - 第1部分](https://www.youtube.com/watch?v=kPylihJRG70)
- [面向黑客的 Linux（别担心，你不需要成为黑客！）](https://www.youtube.com/watch?v=VbEx7B_PTOE)

明天我们开始为期 7 天的网络深入探索，我们将寻求为自己提供围绕 DevOps 的网络基础知识和理解。

[第21天](day21.md)见
