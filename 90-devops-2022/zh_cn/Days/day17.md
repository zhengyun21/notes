---
title: '#90DaysOfDevOps - 文本编辑器 - nano 与 vim - 第17天'
published: false
description: 90DaysOfDevOps - 文本编辑器 - nano 与 vim
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048703
---
## 文本编辑器 - nano 与 vim

你的大多数 Linux 系统都将是服务器，而这些服务器不会有图形界面（GUI）。我在上一节课中也提到，Linux 主要由配置文件组成，要进行更改，你需要能够编辑这些配置文件来修改系统上的任何内容。

有很多选择，但我认为我们应该介绍可能最常见的两个终端文本编辑器。我使用过这两个编辑器，对我来说，我觉得 `nano` 是进行快速更改时的简单选择，但 `vim` 有着非常广泛的功能集。

### nano

- 并非在每个系统上都可用。
- 非常适合入门使用。

如果你运行 `nano 90DaysOfDevOps.txt`，我们将创建一个空的新文件，从这里我们可以添加文本，下方有操作说明告诉我们如何处理这个文件。

![](Images/Day17_Linux1.png)

现在我们可以使用 `control x + enter`，然后运行 `ls`，你现在可以看到我们的新文本文件。

![](Images/Day17_Linux2.png)

现在我们可以对该文件运行 `cat` 来读取内容。然后我们可以使用同样的 `nano 90DaysOfDevOps.txt` 来添加额外的文本或修改你的文件。

对我来说，nano 在进行配置文件的小改动时非常简单易用。

### vim

可能是目前最常见的文本编辑器？作为1976年 UNIX 文本编辑器 vi 的兄弟，vim 提供了大量的功能。

- 几乎在每个 Linux 发行版上都受支持。
- 功能极其强大！你很可能找到一门整整7小时的课程只讲 vim。

我们可以使用 `vim` 命令进入 vim，或者如果我们想编辑我们的新 txt 文件，可以运行 `vim 90DaysOfDevOps.txt`，但你首先会注意到底部缺少帮助菜单。

第一个问题可能是 "如何退出 vim？" 答案是按 `esc`，如果我们没有做任何更改，那么就是 `:q`

![](Images/Day17_Linux3.png)

你以 `normal`（普通）模式启动，还有其他模式：`command`（命令）、`normal`（普通）、`visual`（可视）、`insert`（插入），如果我们想添加文本，需要从 `normal` 切换到 `insert` 模式，需要按 `i`。如果你已经添加了一些文本并想保存这些更改，那么你会按 esc 然后 `:wq`

![](Images/Day17_Linux4.png)

![](Images/Day17_Linux5.png)

你可以使用 `cat` 命令确认你已经保存了这些更改。

vim 有一些很酷的快速功能，如果你知道快捷键，可以让你非常快速地完成一些琐碎的任务，而快捷键本身就是一个课题。假设我们添加了一列重复的单词，现在需要修改它，也许它是一个配置文件，我们重复了一个网络名称，现在这个名称变了，我们想快速修改它。我在这个例子中使用的是单词 day。

![](Images/Day17_Linux6.png)

现在我们想将那个词替换为 90DaysOfDevOps，我们可以通过按 `esc` 并输入 `:%s/Day/90DaysOfDevOps` 来实现。

![](Images/Day17_Linux7.png)

当你按回车时的结果是，单词 day 被替换为 90DaysOfDevOps。

![](Images/Day17_Linux8.png)

复制和粘贴对我来说是一个大开眼界。复制不是 copy，而是 yank（抽出）。我们可以在普通模式下使用 `yy` 来复制。`p` 在同一行粘贴，`P` 在新的一行粘贴。

你也可以通过选择你想删除的行数然后按 `dd` 来删除这些行。

也可能会有你需要搜索文件的时候，现在我们可以使用 `grep`（如之前的课程中提到的），但我们也可以使用 vim。我们可以使用 `/word`，这会找到第一个匹配项，要导航到下一个，你可以使用 `n` 键，以此类推。

对于 vim，这甚至还没有触及表面，我能给的最大建议就是尽可能多地动手使用 vim。

一个常见的面试问题是你在 Linux 中最喜欢的文本编辑器是什么，我建议你至少对这两者都有所了解，这样你就可以回答，说 nano 因为它简单也没问题。至少你能展示你对文本编辑器是什么有所理解。但要动手使用它们以提高熟练度。

另一个在 vim 中导航的提示：我们可以使用 `H, J, K, L` 以及我们的方向键。

## 资源

- [Vim in 100 Seconds](https://www.youtube.com/watch?v=-txKSRn0qeA)
- [Vim tutorial](https://www.youtube.com/watch?v=IiwGbcd8S7I)
- [Learn the Linux Fundamentals - Part 1](https://www.youtube.com/watch?v=kPylihJRG70)
- [Linux for hackers (don't worry you don't need to be a hacker!)](https://www.youtube.com/watch?v=VbEx7B_PTOE)

[第18天](day18.md)见。
