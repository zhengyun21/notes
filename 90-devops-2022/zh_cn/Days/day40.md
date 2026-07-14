---
title: '#90DaysOfDevOps - 代码的社交网络 - 第40天'
published: false
description: 90DaysOfDevOps - 代码的社交网络
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049044
---
## 代码的社交网络
探索 GitHub | GitLab | BitBucket

今天我想介绍一些基于 Git 的服务，这些服务我们可能都听说过，而且预计也在日常使用。

然后，我们将利用之前课程中学到的知识，将我们的数据副本迁移到各个主要服务上。

我把这一节称为“代码的社交网络”，让我解释一下原因？

### GitHub

对我来说最常见的是 GitHub，GitHub 是一个基于 Web 的 Git 托管服务。它被软件开发人员最广泛地用于存储代码。它提供了源代码管理以及 Git 版本控制功能，还有许多附加功能。它允许团队或开源贡献者轻松沟通，并为编码提供了社交属性。（因此有了社交网络这个标题）自2018年以来，GitHub 已成为微软的一部分。

GitHub 已经存在相当长一段时间了，成立于2007/2008年。如今平台上拥有超过4000万用户。

GitHub 主要功能

- 代码仓库（Code Repository）
- 拉取请求（Pull Requests）
- 项目管理工具集 - 议题（Issues）
- CI/CD 流水线 - GitHub Actions

在定价方面，GitHub 为用户提供了多种不同级别的定价。更多信息可以在 [Pricing](https://github.com/pricing) 上找到。

为了本次演示的目的，我们将介绍免费层。

在本演练中我将使用我已创建的 GitHub 账户，如果你还没有账户，那么在 GitHub 首页有一个注册选项，只需几个简单步骤即可完成设置。

### GitHub 首页

当你首次登录 GitHub 账户时，你会看到一个包含很多小部件的页面，为你提供选项，让你选择想看什么或想做什么。首先我们有“所有动态（All Activity）”，这将让你了解你的仓库发生了什么，或与你的组织或账户相关的一般活动。

![](Images/Day40_Git1.png)

接下来是我们的代码仓库，要么是我们自己的，要么是我们最近交互过的仓库。我们还可以快速创建新仓库或搜索仓库。

![](Images/Day40_Git2.png)

然后是我们最近的活动，对我来说是我最近创建或参与的议题（issues）和拉取请求（pull requests）。

![](Images/Day40_Git3.png)

在页面右侧，有一些我们可能感兴趣的仓库推荐，很可能是基于你最近的活动或你自己的项目。

![](Images/Day40_Git4.png)

说实话，我很少在我刚才看到和描述的首页上停留，尽管我现在看到这个动态流真的很有用，可以帮助更好地与社区在某些项目上进行互动。

接下来，如果我们想进入 GitHub 个人资料页面，我们可以导航到右上角，在你的头像上有一个下拉菜单，允许你浏览你的账户。从这里要访问你的个人资料，请选择“Your Profile”。

![](Images/Day40_Git5.png)

接下来，你的个人资料页面将出现，默认情况下，除非你更改配置，否则你不会看到我所拥有的内容，我添加了一些功能来显示我在 [vZilla](https://vzilla.co.uk) 上的最新博客文章，以及我的 [YouTube](https://m.youtube.com/c/MichaelCade1) 频道上的最新视频。

就个人而言，你不会花太多时间看自己的个人资料，但这是一个很好的个人资料页面，可以在你的网络中分享，让他们看到你正在做的酷炫项目。

![](Images/Day40_Git6.png)

然后我们可以深入了解 GitHub 的构建基础——仓库。在这里，你将看到自己的仓库，如果你有私有仓库，它们也会显示在这个长列表中。

![](Images/Day40_Git7.png)

由于仓库对 GitHub 如此重要，让我选择一个最近相当活跃的仓库，并介绍一些我们可以在这里使用的核心功能，除此之外，我已经在本地系统上使用 git 编辑我们的“代码”时所使用的一切。

首先，从上一个窗口我选择了 90DaysOfDevOps 仓库，我们看到了这个视图。你可以从这个视图中看到很多信息，中间是我们的主要代码结构，显示存储在我们仓库中的文件和文件夹。底部显示我们的 readme.md。在页面右侧有一个关于部分，仓库有描述和用途。然后下面有很多信息，显示有多少人星标（starred）了该项目、分叉（forked）和关注（watching）。

![](Images/Day40_Git8.png)

如果我们再往下滚动一点，你还会看到我们有发布版本（Releases），这些来自挑战的 golang 部分。我们的项目中没有任何包（packages），这里列出了我们的贡献者。（感谢社区协助我进行拼写检查和事实核查）然后我们还有使用的语言，这些同样来自挑战的不同部分。

![](Images/Day40_Git9.png)

在页面顶部，你会看到一系列标签页。这些可能会有所不同，而且可以修改，只显示你需要的。你会看到这里我并没有使用所有这些标签页，我应该删除它们，以确保我的整个仓库整洁。

首先是我们刚刚讨论过的代码标签页，但这些标签页在浏览仓库时始终可用，这非常有用，因此我们可以快速轻松地在各个部分之间跳转。接下来是议题（Issues）标签页。

议题让你在 GitHub 上跟踪工作，也就是开发发生的地方。在这个特定的仓库中，你可以看到我有一些关于添加图表或拼写错误的议题，但我们还有一个议题，说明需要或要求提供该仓库的中文版本。

如果这是一个代码仓库，那么这是一个向维护者提出问题或议题的好地方，但请记住，对你报告的内容要留意并详细说明，尽可能提供详细信息。

![](Images/Day40_Git10.png)

下一个标签页是拉取请求（Pull Requests），拉取请求让你告诉其他人你已经推送到仓库某个分支的更改。这里可能是有人分叉（forked）了你的仓库，进行了更改，例如错误修复或功能增强，或者只是拼写错误——在这个仓库中很多时候都是这种情况。

我们稍后会更详细地介绍分叉（forking）。

![](Images/Day40_Git11.png)

我相信下一个标签页是相当新的？但我认为对于像 #90DaysOfDevOps 这样的项目，这真的可以帮助指导内容之旅，还可以帮助社区在他们自己的学习之旅中前进。我为挑战的每个部分创建了一些讨论组，以便人们可以加入并讨论。

![](Images/Day40_Git12.png)

Actions 标签页将使你能够从 GitHub 内部构建、测试和部署代码等等。GitHub Actions 将是我们在挑战的 CI/CD 部分中介绍的内容，但这里我们可以设置一些配置来自动化步骤。

在我的主要 GitHub 个人资料上，我正在使用 GitHub Actions 获取最新的博客文章和 YouTube 视频，以保持该主页的更新。

![](Images/Day40_Git13.png)

我上面提到 GitHub 不仅仅是一个源代码仓库，它也是一个项目管理工具，Project 标签页使我们能够构建项目表和看板（kanban）类型的面板，以便我们可以将议题（Issues）和拉取请求（PRs）关联起来，更好地协作项目，并对这些任务有可见性。

![](Images/Day40_Git14.png)

我知道议题对我来说似乎是记录功能请求的好地方，它们确实是，但 wiki 页面允许为项目制定全面的路线图，概述当前状态，并总体上更好地记录你的项目，无论是故障排除还是操作指南类型的内容。

![](Images/Day40_Git15.png)

对于这个项目来说不太适用，但 Security 标签页确实存在，以确保贡献者知道如何处理某些任务，我们可以在这里定义策略，还可以进行代码扫描插件，以确保你的代码例如不包含秘密的环境变量。

![](Images/Day40_Git16.png)

对我来说，Insights 标签页很棒，它提供了关于仓库的大量信息，从发生了多少活动到提交和议题，但它还报告了仓库的流量。你可以在左侧看到一个列表，允许你深入了解仓库的各项指标。

![](Images/Day40_Git17.png)

最后我们有 Settings 标签页，这是我们可以深入了解如何运行仓库的地方，我目前是该仓库的唯一维护者，但我们可以在这里共享这个职责。我们可以在这里定义集成和其他此类任务。

![](Images/Day40_Git18.png)

这是对 GitHub 的一个超级快速的概述，我认为我可能提到了一些其他需要更详细解释的领域。如前所述，GitHub 托管着数百万个仓库，其中大部分都持有源代码，这些仓库可以是公开或私下访问的。

### 分叉（Forking）

明天我会更深入地介绍开源（Open-Source），但任何代码仓库的一个重要部分是与社区协作的能力。让我们设想一个场景：我想要一个仓库的副本，因为我想对它做一些更改，也许我想修复一个错误，或者我想更改某些东西以用于我自己的用例，而这可能不是代码原始维护者所设想的用例。这就是我们所说的分叉（forking）一个仓库。分叉（fork）是仓库的一个副本。分叉一个仓库允许你自由地进行更改实验，而不会影响原始项目。

让我回到登录后的首页，看看那些推荐的仓库之一。

![](Images/Day40_Git19.png)

如果我们点击那个仓库，我们会得到与我们刚刚在 90DaysOfDevOps 仓库中走过的一样的外观。

![](Images/Day40_Git20.png)

如果我们注意下方，我们有3个选项：watch、fork 和 star。

- Watch - 当仓库发生事情时接收更新。
- Fork - 仓库的一个副本。
- Star - “我觉得你的项目很酷”

![](Images/Day40_Git21.png)

鉴于我们想要一个该仓库的副本来工作的场景，我们将点击 fork 选项。如果你是多个组织的成员，那么你必须选择分叉将在哪里进行，我将选择我的个人资料。

![](Images/Day40_Git22.png)

现在我们有了自己的仓库副本，我们可以自由地工作和按我们认为合适的方式进行更改。这将是我们之前简要提到的拉取请求（pull request）流程的开始，但我们明天会更详细地介绍。

![](Images/Day40_Git23.png)

好吧，我听到你说，但是如果它在网站上，我如何对这个仓库和代码进行更改呢，好吧，你可以通过网站进行编辑，但这与你使用本地系统上最喜欢的 IDE 和你最喜欢的颜色主题是不一样的。为了让我们在本地机器上获得这个仓库的副本，我们将执行仓库的克隆（clone）。这将允许我们在本地工作，然后将我们的更改推回到我们分叉的仓库副本中。

在获取这份代码副本时，我们有几个选项，如下所示。

有一个本地版本的 GitHub Desktop，它为你提供了一个可视化的桌面应用程序来跟踪更改，并在本地和 GitHub 之间推送和拉取更改。

为了这个小演示的目的，我将使用我们在上面看到的 HTTPS 网址。

![](Images/Day40_Git24.png)

现在在我们的本地机器上，我将导航到一个我愿意下载此仓库的目录，然后运行 `git clone url`。

![](Images/Day40_Git25.png)

现在我们可以用 VSCode 来真正做一些更改。

![](Images/Day40_Git26.png)

现在让我们做一些更改，我想更改所有这些链接并用其他东西替换它们。

![](Images/Day40_Git27.png)

现在如果我们回到 GitHub 并在该仓库中找到我们的 readme.md，你应该能够看到我对该文件所做的一些更改。

![](Images/Day40_Git28.png)

在这个阶段，这可能是完整的，我们可能对我们的更改感到满意，因为我们是唯一会使用我们新更改的人，但也许这是一个错误修复，如果是这样的话，那么我们将希望通过拉取请求（Pull Request）进行贡献，以通知原始仓库维护者我们的更改，并看看他们是否接受我们的更改。

我们可以通过使用下面高亮显示的 contribute 按钮来做到这一点。明天当我们研究开源工作流时，我会更多地介绍这一点。

![](Images/Day40_Git29.png)

我花了很长时间研究 GitHub，我听到你们中的一些人大喊，但其他选项呢！

嗯，确实有，我将找到一些资源来涵盖其中一些基础知识。在你的旅途中，你还会遇到 GitLab 和 BitBucket 等，虽然它们都是基于 Git 的服务，但它们各有不同。

你还会遇到托管选项。在这里最常见的是我看到 GitLab 作为托管版本，与 GitHub Enterprise 相对（不相信有免费的托管 GitHub？）


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


[第41天](day41.md)见。
