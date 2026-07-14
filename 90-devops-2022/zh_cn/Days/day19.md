---
title: '#90DaysOfDevOps - 使用 Bash 脚本自动化任务 - 第19天'
published: false
description: 90DaysOfDevOps - 使用 Bash 脚本自动化任务
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048774
---
## 使用 Bash 脚本自动化任务

我们今天要使用的 shell 是 bash，不过明天深入 ZSH 时会再介绍另一种 shell。

BASH - **B**ourne **A**gain **Sh**ell

我们几乎可以为 shell 脚本编写一个完整的 7 天章节，就像编程语言那样。bash 让我们能够与其他自动化工具协同工作来完成任务。

我现在仍然会和很多人交流，他们编写了一些复杂的 shell 脚本来实现某些功能，并依赖这些脚本来处理业务中最重要的事情。我并不是说我们需要为了这个目的去理解 shell/bash 脚本，这不是正确的方式。但我们应该学习 shell/bash 脚本，以便与我们的自动化工具协同工作，以及处理临时任务。

我们在本章节中使用过的一个例子是 VAGRANTFILE，我们用它来创建 VM。我们可以将其包装成一个简单的 bash 脚本，每周一早上删除并重新创建，这样我们每周都能获得一个全新的 Linux VM 副本。我们还可以在该 Linux 机器上添加所有需要的软件栈，等等，全部通过这一个 bash 脚本来完成。

我认为还有一件事我至少经常听到，那就是脚本编写的实操问题在面试中越来越普遍。

### 入门

就像我们在这整个 90 天中涉及的很多事情一样，唯一真正的学习方式就是通过实践。动手经验将帮助你将所有这些内容融入肌肉记忆。

首先，我们需要一个文本编辑器。在 [第17天](Day17.md) 中，我们介绍了可能最常见的两种文本编辑器以及如何使用它们的一些基础知识。

让我们直接进入主题，创建我们的第一个 shell 脚本。

`touch 90DaysOfDevOps.sh`

接着运行 `nano 90DaysOfDevOps.sh`，这会在 nano 中打开我们新的空白 shell 脚本。同样，你也可以在这里选择自己喜欢的文本编辑器。

所有 bash 脚本的第一行需要类似这样：`#!/usr/bin/bash`，这是指向你的 bash 二进制文件的路径。

不过，你应该通过在终端运行 `which bash` 来检查这一点。如果你使用的不是 Ubuntu，也可以尝试在终端中运行 `whereis bash`。

然而，你可能会在已创建的 shell 脚本中看到其他路径，包括：

- `#!/bin/bash`
- `#!/usr/bin/env bash`

在脚本的下几行，我喜欢添加注释，说明脚本的用途或者至少一些关于我的信息。你可以使用 `#` 来实现。这允许我们注释掉代码中的特定行，并为即将执行的命令提供描述。我发现注释越多越好，特别是对用户体验而言，尤其是当你要分享这个脚本时。

我有时会使用 figlet，这是我们在 Linux 章节前面安装过的一个程序，在我们的脚本中创建一些 ASCII 艺术来开启脚本。

![](Images/Day19_Linux1.png)

我们在 Linux 章节前面（[第15天](Day15.md)）学过的所有命令都可以在这里作为一个简单的命令来测试我们的脚本。

让我们向脚本中添加一个简单的代码块。

``` 
mkdir 90DaysOfDevOps
cd 90DaysOfDevOps
touch Day19
ls 
```

然后你可以保存并退出文本编辑器。如果我们用 `./90DaysOfDevOps.sh` 运行脚本，你应该会收到一个权限被拒绝的消息。你可以使用 `ls -al` 命令检查此文件的权限，你会看到高亮显示的部分，我们没有该文件的执行权限。

![](Images/Day19_Linux2.png)

我们可以使用 `chmod +x 90DaysOfDevOps.sh` 来更改这一点，然后你会看到 `x`，这意味着我们现在可以执行我们的脚本了。

![](Images/Day19_Linux3.png)

现在我们可以再次使用 `./90DaysOfDevOps.sh` 运行我们的脚本。运行脚本后，它现在已经创建了一个新目录，进入了该目录，然后创建了一个新文件。

![](Images/Day19_Linux4.png)

这是非常基础的内容，但希望你能开始看到如何将其用于调用其他工具，作为让你生活更轻松和实现自动化的一种方式。

### 变量、条件语句

这部分的很多内容实际上是我们学习 Golang 时讲过的重复内容，但我认为值得我们在这里再深入一下。

- ### 变量

变量使我们能够在整个可能很复杂的脚本中，一次性定义某个特定的重复使用的术语。

要在脚本中添加变量，你只需像这样在干净的一行中添加：

`challenge="90DaysOfDevOps"`

这样，当我们在代码中使用 `$challenge` 时，如果我们更改变量，它会在整个脚本中反映出来。

![](Images/Day19_Linux5.png)

如果我们现在运行我们的 `sh` 脚本，你会看到添加到脚本中的打印输出。

![](Images/Day19_Linux5.png)

我们还可以使用以下方式请求用户输入来设置我们的变量：

``` 
echo "Enter your name"
read name
```

这会将输入定义为变量 `$name`，我们可以在后面使用它。

- ### 条件语句

也许我们想知道谁参加了我们的挑战，以及他们完成了多少天。我们可以使用 `if` `if-else` `else-if` 条件语句来定义这一点，以下是我们脚本中定义的内容。

```
#!/bin/bash
#  ___   ___  ____                   ___   __ ____              ___
# / _ \ / _ \|  _ \  __ _ _   _ ___ / _ \ / _|  _ \  _____   __/ _ \ _ __  ___
#| (_) | | | | | | |/ _` | | | / __| | | | |_| | | |/ _ \ \ / / | | | '_ \/ __|
# \__, | |_| | |_| | (_| | |_| \__ \ |_| |  _| |_| |  __/\ V /| |_| | |_) \__ \
#   /_/ \___/|____/ \__,_|\__, |___/\___/|_| |____/ \___| \_/  \___/| .__/|___/
#                         |___/                                     |_|
#
# This script is to demonstrate bash scripting!

# Variables to be defined

ChallengeName=#90DaysOfDevOps
TotalDays=90

# User Input

echo "Enter Your Name"
read name
echo "Welcome $name to $ChallengeName"
echo "How Many Days of the $ChallengeName challenge have you completed?"
read DaysCompleted

if [ $DaysCompleted -eq 90 ]
then
  echo "You have finished, well done"
elif [ $DaysCompleted -lt 90 ]
then
  echo "Keep going you are doing great"
else
  echo "You have entered the wrong amount of days"
fi
```

从上面的代码中你也可以看到，我们正在运行一些比较或检查值，以进入下一阶段。我们这里有一些不同的选项值得注意。

- `eq` - 如果两个值相等，则返回 TRUE
- `ne` - 如果两个值不相等，则返回 TRUE
- `gt` - 如果第一个值大于第二个值，则返回 TRUE
- `ge` - 如果第一个值大于或等于第二个值，则返回 TRUE
- `lt` - 如果第一个值小于第二个值，则返回 TRUE
- `le` - 如果第一个值小于或等于第二个值，则返回 TRUE

我们还可以使用 bash 脚本来确定有关文件和文件夹的信息，这被称为文件条件。

- `-d file` 如果文件是目录，则为 True
- `-e file` 如果文件存在，则为 True
- `-f file` 如果提供的字符串是文件，则为 True
- `g file` 如果文件上设置了组 ID，则为 True
- `-r file` 如果文件可读，则为 True
- `-s file` 如果文件大小非零，则为 True

```
FILE="90DaysOfDevOps.txt"
if [ -f "$FILE" ]
then 
  echo "$FILE is a file"
else 
  echo "$FILE is not a file"
fi
```

![](Images/Day19_Linux7.png)

只要该文件仍在我们的目录中，我们就应该收到第一个 echo 命令的返回。但如果我们删除该文件，那么我们应该收到第二个 echo 命令。

![](Images/Day19_Linux8.png)

希望你能看到，当你在系统中搜索特定项目时，这如何能够为你节省时间。

我在 GitHub 上发现了一个很棒的仓库，里面似乎有无穷无尽的脚本 [DevOps Bash Tools](https://github.com/HariSekhon/DevOps-Bash-tools/blob/master/README.md)

### 示例

**场景**：我们有一家公司叫 "90DaysOfDevOps"，我们已经运营了一段时间，现在是时候在未来几周内将团队从 1 个人扩展到更多人了。到目前为止，我是唯一知道入职流程的人，所以我们希望通过自动化其中一些任务来减少这个瓶颈。

**需求**：
- 可以将用户作为命令行参数传入。
- 使用命令行参数的名称创建用户。
- 可以将密码作为命令行参数传入。
- 为用户设置密码
- 显示账户创建成功的消息。

让我们开始用 `touch create_user.sh` 创建我们的 shell 脚本。

在继续之前，让我们也使用 `chmod +x create_user.sh` 使其可执行。

然后我们可以使用 `nano create_user.sh` 开始编辑我们的脚本，以应对我们被设定的场景。

我们可以看一下第一个需求"可以将用户作为命令行参数传入"，我们可以使用以下内容：

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1"
```

![](Images/Day19_Linux9.png)

继续并使用 `./create_user.sh Michael` 运行这个脚本，运行脚本时将 Michael 替换为你的名字。

![](Images/Day19_Linux10.png)

接下来我们可以处理第二个需求"使用命令行参数的名称创建用户"，这可以通过 `useradd` 命令完成。`-m` 选项用于将用户主目录创建为 /home/username。

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1 user account being created."

#A user is created with the name of command line argument
sudo useradd -m "$1"

```

警告：如果你没有提供用户账户名，那么它会报错，因为我们没有填充变量 `$1`。

然后我们可以使用 `awk -F: '{ print $1}' /etc/passwd` 命令检查该账户是否已创建。

![](Images/Day19_Linux11.png)

我们的下一个需求是"可以将密码作为命令行参数传入"。首先，我们在生产环境中永远不会这样做，这更多是让我们在实验室中通过一系列需求来理解。

```
#! /usr/bin/bash

#A user can be passed in as a command line argument
echo "$1 user account being created."

#A user is created with the name of command line argument
sudo useradd -m "$1"

#A password can be parsed in as a command line argument.
sudo chpasswd <<< "$1":"$2"
```

如果我们然后用两个参数运行这个脚本 `./create_user.sh 90DaysOfDevOps password`

你可以从下面的图片中看到，我们执行了脚本，创建了用户和密码，然后我们手动切换到该用户并用 `whoami` 命令确认。

![](Images/Day19_Linux12.png)

最后一个需求是"显示账户创建成功的消息"。实际上，这已经在我们的代码第一行中了，我们可以在上面的截图中看到 `90DaysOfDevOps user account being created` 已经显示了。这是我们在用 `$1` 参数测试时留下的。

现在这个脚本可以用来快速 onboard 并设置新用户到我们的 Linux 系统上。但也许，与其让一些老员工来处理这个，然后再让其他人获取他们的新用户名或密码，我们可以添加一些用户输入，就像我们前面讲过的那样，来捕获我们的变量。

``` 
#! /usr/bin/bash

echo "What is your intended username?"
read  username
echo "What is your password"
read  password

#A user can be passed in as a command line argument
echo "$username user account being created."

#A user is created with the name of command line argument
sudo useradd -m $username

#A password can be parsed in as a command line argument.
sudo chpasswd <<< $username:$password
```

通过这些更具交互性的步骤，

![](Images/Day19_Linux14.png)

为了结束这部分，也许我们确实想输出一个成功的结果，说明我们的新用户账户已经完成创建。

![](Images/Day19_Linux15.png)

我注意到的一件事是，我们在输入时显示了密码，我们可以通过使用 `-s` 标志来隐藏它，代码行为 `read -s password`。

![](Images/Day19_Linux16.png)

如果你想删除为实验室目的创建的用户，你可以使用 `sudo userdel test_user`。

[示例脚本](Linux/create-user.sh)

我再次声明，这并不是说你会在日常工作中创建这样的东西，但我认为这是可以突出 shell 脚本灵活性的一种方式。

想想你每天、每周或每月做的任何可重复的任务，以及如何更好地自动化它，第一个选择可能是使用 bash 脚本，然后再进入更复杂的领域。

我创建了一个非常简单的 bash 文件，帮助我在本地机器上使用 minikube 启动 Kubernetes 集群，以及数据服务和 Kasten K10，以帮助演示数据管理方面的需求和需要。[Project Pace](https://github.com/MichaelCade/project_pace/blob/main/singlecluster_demo.sh) 但我觉得在这里提这个不太合适，因为我们还没有涉及 Kubernetes。

## 相关资料

- [100秒了解 Bash](https://www.youtube.com/watch?v=I4EWvMFj37g)
- [Bash 脚本与实用示例 - 完整课程](https://www.youtube.com/watch?v=TPRSJbtfK4M)
- [SSH 图形客户端 - Remmina](https://remmina.org/)
- [SSH 初学者指南](https://www.youtube.com/watch?v=2QXkrLVsRmk)
- [100秒了解 Vim](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim 教程](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [学习 Linux 基础 - 第1部分](https://www.youtube.com/watch?v=kPylihJRG70)
- [面向黑客的 Linux（别担心，你不需要成为黑客！）](https://www.youtube.com/watch?v=VbEx7B_PTOE)

[第20天](day20.md)见
