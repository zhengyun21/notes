---
title: '#90DaysOfDevOps - 开源工作流 - 第41天'
published: false
description: 90DaysOfDevOps - 开源工作流
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048806
---
## 开源工作流

希望通过对 Git 的7个部分的学习，我们对 git 是什么以及像 GitHub 这样的基于 git 的服务如何与 git 集成以提供源代码仓库有了更好的理解，同时也提供了一种让更广泛的社区可以一起协作代码和项目的方式。

当我们在学习 GitHub 基础知识时，我们经历了分叉一个随机项目并更改我们本地仓库的过程。在这里，我们想更进一步，为一个开源项目做出贡献。请记住，贡献不一定是错误修复、编码功能，它也可以是文档。每一份小小的帮助都很重要，它也让你能够亲自动手实践我们介绍过的一些 git 功能。

## 分叉一个项目

我们要做的第一件事是找到一个我们可以贡献的项目。我最近一直在 [Kanister Project](https://github.com/kanisterio/kanister) 上做演示，我想把现在已经在 YouTube 上的演示文稿分享到这个项目的主 readme.md 文件中。

首先我们需要分叉这个项目。让我们来完成这个流程。我将导航到上面分享的链接并分叉该仓库。

![](Images/Day41_Git1.png)

现在我们有了一整个仓库的副本。

![](Images/Day41_Git2.png)

作为 Readme.md 文件的参考，原来列出的演示文稿只有这两个，所以我们需要通过我们的流程来修复这个问题。

![](Images/Day41_Git3.png)

## 克隆到本地机器

现在我们有了自己的分叉，我们可以把它拉取到本地，然后我们可以开始编辑文件。使用我们仓库上的 code 按钮，我们可以获取 URL，然后在我们希望放置仓库的目录中使用 `git clone url`。

![](Images/Day41_Git4.png)

## 进行我们的更改

我们的项目已经在本地，因此我们可以打开 VSCode 或你选择的 IDE 或文本编辑器来添加你的修改。

![](Images/Day41_Git5.png)

readme.md 文件是用 markdown 语言编写的，因为我正在修改别人的项目，我将遵循现有项目的格式来添加我们的内容。

![](Images/Day41_Git6.png)

## 测试你的更改

作为最佳实践，我们必须测试我们的更改，这完全说得通，如果这是对应用程序的代码更改，你会希望确保应用程序在代码更改后仍能正常运行，那么我们也必须确保文档格式正确且看起来没问题。

在 VSCode 中，我们可以添加很多插件，其中之一就是预览 markdown 页面的能力。

![](Images/Day41_Git7.png)

## 将更改推回到我们分叉的仓库

我们没有权限直接将更改推回到 Kanister 仓库，所以我们必须走这条路线。现在我对我们的更改感到满意，我们可以运行一些现在已非常熟悉的 git 命令。

![](Images/Day41_Git8.png)

现在我们回到 GitHub 再次检查更改，然后贡献回主项目。

看起来不错。

![](Images/Day41_Git9.png)

现在我们可以回到 Kanister 分叉仓库的顶部，我们可以看到我们比 kanisterio:master 分支领先1个提交。

![](Images/Day41_Git10.png)

接下来我们点击上面高亮显示的 contribute 按钮。我们看到“Open Pull Request”选项。

![](Images/Day41_Git11.png)

## 打开一个拉取请求

在下一张图片中发生的事情 quite 多，左上角你现在可以看到我们在原始或主仓库中。然后你可以看到我们正在比较什么，那就是原始主仓库和我们分叉的仓库。然后我们有一个创建拉取请求的按钮，我们稍后会回来点击它。我们有我们的单个提交，但如果这是更多的更改，你可能会在这里有多个提交。然后我们有我们在 readme.md 文件中做的更改。

![](Images/Day41_Git12.png)

我们已经审查了上述更改，我们准备创建拉取请求，点击绿色按钮。

然后，取决于项目维护者如何在其仓库上设置拉取请求功能，你可能会有也可能没有一个模板，该模板会给你一些指示，告诉你维护者希望看到什么。

在这里，你再次希望对你的工作做出有意义的描述，清晰简洁但有足够的细节。你可以看到我做了一个简单的更改概述，并勾选了文档。

![](Images/Day41_Git13.png)

## 创建拉取请求

我们现在准备创建我们的拉取请求。点击页面顶部的“Create Pull Request”后，你会得到你的拉取请求摘要。

![](Images/Day41_Git14.png)

向下滚动，你可能会看到一些自动化正在发生，在这种情况下，我们需要审查，并且一些检查正在进行中。我们可以看到 Travis CI 正在进行中，构建已经开始，这将检查我们的更新，确保在合并任何东西之前，我们不会用我们的添加破坏任何东西。

![](Images/Day41_Git15.png)

另一件要注意的事情是，上面截图中的红色可能看起来有点吓人，看起来好像你犯了错误！别担心，你没有破坏任何东西，我这里最大的建议是，这个流程是为了帮助你和项目的维护者。如果你犯了错误，至少根据我的经验，维护者会联系并建议下一步该做什么。

这个拉取请求现在对所有人公开可见 [added Kanister presentation/resource #1237](https://github.com/kanisterio/kanister/pull/1237)

我将在合并和拉取请求被接受之前发布这篇文章，所以也许我们可以为任何仍在关注并能够添加成功 PR 图片的人提供一点奖励？

1. 将这个仓库分叉到你自己的 GitHub 账户
2. 添加你的图片和可能的文字
3. 将更改推送到你分叉的仓库
4. 创建一个我会看到并批准的 PR。
5. 我会想一些奖励

这就结束了我们对 Git 和 GitHub 的探讨，接下来我们将深入容器，首先宏观地了解容器的原理、原因，以及虚拟化技术和我们如何走到今天这一步。

## 相关资料

- [Learn GitLab in 3 Hours | GitLab Complete Tutorial For Beginners](https://www.youtube.com/watch?v=8aV5AxJrHDg)
- [BitBucket Tutorials Playlist](https://www.youtube.com/watch?v=OMLh-5O6Ub8&list=PLaD4FvsFdarSyyGl3ooAm-ZyAllgw_AM5)
- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)
- [Git cheatsheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)


[第42天](day42.md)见。
