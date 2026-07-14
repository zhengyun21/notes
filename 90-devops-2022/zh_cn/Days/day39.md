---
title: '#90DaysOfDevOps - 查看、取消暂存、丢弃与恢复 - 第39天'
published: false
description: '90DaysOfDevOps - 查看、取消暂存、丢弃与恢复'
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048827
---
## 查看、取消暂存、丢弃与恢复

从昨天结束的地方继续，围绕 git 的一些命令以及如何在项目中利用 git。请记住，我们还没有接触 GitHub 或任何其他基于 git 的服务，这一切都是为了帮助你目前在本地控制项目，但当我们开始集成到这些工具时，它们都会变得有用。

### 查看已暂存和未暂存的更改

在提交之前，确保查看已暂存和未暂存的代码是一种良好的实践。我们可以通过运行 `git diff --staged` 命令来做到这一点

![](Images/Day39_Git1.png)

然后它会向我们显示我们所做的所有更改以及我们添加或删除的所有新文件。

修改文件中的更改用 `---` 或 `+++` 表示，你可以在下图中看到，我们只是添加了 +add some text below，这意味着它们是新行。

![](Images/Day39_Git2.png)

我们还可以运行 `git diff` 来比较我们的暂存区与工作目录。如果我们对刚刚添加的文件 code.txt 做一些更改并添加几行文本。

![](Images/Day39_Git3.png)

如果我们然后运行 `git diff`，我们会进行比较并看到下面的输出。

![](Images/Day39_Git4.png)

### 可视化差异工具（Visual Diff Tools）

对我来说，上面的内容更令人困惑，所以我更愿意使用可视化工具，

举几个可视化差异工具的例子：

- KDiff3
- P4Merge
- WinMerge（仅 Windows）
- VSCode

要在 git 中设置它，我们运行以下命令 `git config --global diff.tool vscode`

我们将运行上述命令，并在启动 VSCode 时设置一些参数。

![](Images/Day39_Git5.png)

我们还可以用 `git config --global -e` 检查我们的配置

![](Images/Day39_Git6.png)

然后我们可以使用 `git difftool` 来打开我们的差异可视化工具。

![](Images/Day39_Git7.png)

然后它会在差异页面上打开我们的 VSCode 编辑器并比较两者，我们只修改了一个文件，从什么都没有到现在在右侧添加了一行代码。

![](Images/Day39_Git8.png)

我发现这种方法更容易跟踪更改，这与我们查看基于 git 的服务（例如 GitHub）时会看到的内容类似。

我们还可以使用 `git difftool --staged` 来比较暂存区与已提交的文件。

![](Images/Day39_Git9.png)

然后我们可以在提交之前循环浏览我们更改的文件。

![](Images/Day39_Git10.png)

我使用 VSCode 作为我的 IDE，像大多数 IDE 一样，它们内置了这个功能，如果你由于某种原因没有安装 IDE，从终端运行这些命令很有帮助，尽管很少需要这样做。

### 查看历史记录

我们之前接触过 `git log`，它将提供我们仓库中所有提交的综合视图。

![](Images/Day39_Git11.png)

每次提交都有自己的十六进制字符串，对仓库来说是唯一的。在这里，你可以看到我们在哪个分支上工作，以及作者、日期和提交消息。

我们还有 `git log --oneline`，它给我们一个更短的十六进制字符串版本，我们可以在其他 `diff` 命令中使用它。我们还只有一行的描述或提交消息。

![](Images/Day39_Git12.png)

我们可以通过运行 `git log --oneline --reverse` 将其反转，从第一次提交开始，现在我们在页面顶部看到我们的第一次提交。

![](Images/Day39_Git13.png)

### 查看提交

能够查看提交消息很好，如果你一直遵循最佳实践并添加了有意义的提交消息，但是还有一个 `git show` 命令，允许我们检查和查看提交。

我们可以使用 `git log --oneline --reverse` 获取我们的提交列表。然后我们可以获取这些提交并运行 `git show <commit ID>`

![](Images/Day39_Git14.png)

该命令的输出将如下所示，包含提交的详细信息、作者以及实际更改的内容。

![](Images/Day39_Git15.png)

我们还可以使用 `git show HEAD~1`，其中 1 表示我们要从当前版本返回多少步。

如果你想获取文件的详细信息，这很好，但如果我们想列出整个快照目录的树的文件。我们可以通过使用 `git ls-tree HEAD~1` 命令来实现，同样是从最后一次提交返回一个快照。我们可以在下面看到我们有两个 blob，这些表示文件，而 tree 将表示目录。你还可以在此信息中看到提交和标签。

![](Images/Day39_Git16.png)

然后我们可以使用上面的结果来深入查看我们文件（blob）的内容，使用 `git show` 命令。

![](Images/Day39_Git17.png)

然后会显示该特定版本文件的内容。

![](Images/Day39_Git18.png)

### 取消暂存文件（Unstaging Files）

有时候，你可能使用了 `git add .`，但实际上有一些文件你暂时不希望提交到那个快照中。在下面的示例中，我将 newfile.txt 添加到了我的暂存区，但我还没准备好提交这个文件，所以我将使用 `git restore --staged newfile.txt` 来撤消 `git add` 步骤。

![](Images/Day39_Git19.png)

我们也可以对修改过的文件（例如 main.js）执行相同的操作并取消暂存提交，见上图我们有一个绿色的 M 表示已修改，然后在下面我们正在取消暂存这些更改。

![](Images/Day39_Git20.png)

我实际上发现这个命令在 90DaysOfDevOps 期间非常有用，因为我有时会超前于当前天数工作，在那里我觉得我想为第二天做笔记，但我不想提交并推送到公共 GitHub 仓库。

### 丢弃本地更改

有时我们可能做出了更改，但我们对这些更改不满意，我们想丢弃它们。我们将再次使用 `git restore` 命令，我们将能够从我们的快照或以前的版本中恢复文件。我们可以针对我们的目录运行 `git restore .`，我们将从快照中恢复所有内容，但请注意，我们的未跟踪文件仍然存在。没有名为 newfile.txt 的以前被跟踪的文件。

![](Images/Day39_Git21.png)

现在要删除 newfile.txt 或任何未跟踪的文件。我们可以使用 `git clean`，我们会得到一个单独的警告。

![](Images/Day39_Git22.png)

或者，如果我们知道后果，那么我们可能想运行 `git clean -fd` 来强制删除所有目录。

![](Images/Day39_Git23.png)

### 将文件恢复到早期版本

正如我们在整篇文章中提到的，Git 能够帮助的一个重要部分是能够从你的快照中恢复文件的副本（这不是备份，但它是一个非常快速的恢复点）。我的建议是，你还应该使用备份解决方案将代码副本保存在其他位置。

举个例子，让我们去删除目录中最重要的文件，注意我们使用的是基于 Unix 的命令将其从目录中删除，而不是 git 命令。

![](Images/Day39_Git24.png)

现在我们的工作目录中没有 readme.md。我们可以使用 `git rm readme.md`，这将反映在我们的 git 数据库中。让我们也从这里删除它来模拟它被完全删除。

![](Images/Day39_Git25.png)

现在让我们用一条消息提交这个操作，并证明我们的工作目录或暂存区中没有任何内容。

![](Images/Day39_Git26.png)

犯了错误，我们现在需要这个文件回来！

我们可以使用 `git undo` 命令来撤消最后一次提交，但如果这是一段时间之前的事情呢？我们可以使用 `git log` 命令来找到我们的提交，然后我们发现我们的文件在上一次提交中，但我们不想撤消所有这些提交，所以我们可以使用这个命令 `git restore --source=HEAD~1 README.md` 来专门找到文件并从我们的快照中恢复它。

你可以看到，使用这个过程，我们现在在工作目录中找回了文件。

![](Images/Day39_Git27.png)

我们现在有一个新的未跟踪文件，我们可以使用前面提到的命令来跟踪、暂存和提交我们的文件和更改。

### Rebase vs Merge

这似乎是在使用 Git 时最令人头疼的问题，何时在你的 git 仓库中使用 rebase 与使用 merge。

首先要知道的是，`git rebase` 和 `git merge` 解决的是同一个问题。两者都是将一个分支的更改集成到另一个分支中。然而，它们以不同的方式做到这一点。

让我们从一个新专用分支中的新功能开始。Main 分支继续进行新的提交。

![](Images/Day39_Git28.png)

这里简单的选项是使用 `git merge feature main`，这将把 main 分支合并到 feature 分支中。

![](Images/Day39_Git29.png)

合并很容易，因为它是非破坏性的。现有分支不会以任何方式更改。然而，这也意味着每次需要合并上游更改时，feature 分支都会有一个无关紧要的合并提交。如果 main 非常繁忙或活跃，这将会或可能污染 feature 分支的历史记录。

作为替代选项，我们可以使用以下命令将 feature 分支 rebase 到 main 分支上

```
git checkout feature
git rebase main
```
这将移动 feature 分支（整个 feature 分支），有效地合并 main 中的所有新提交。但是，rebase 不是使用合并提交，而是通过为原始分支中的每个提交创建全新的提交来重写项目历史记录。

![](Images/Day39_Git30.png)

rebase 的最大好处是项目历史记录更加清晰。它还消除了不必要的合并提交。正如你比较最后两张图片时看到的，你可以沿着一条更加清晰的线性项目历史记录进行追踪。

尽管这仍然不是必然的结论，因为选择更清晰的历史记录也伴随着权衡，如果你不遵循 [rebase 的黄金法则](https://www.atlassian.com/git/tutorials/merging-vs-rebasing#the-golden-rule-of-rebasing)，重写项目历史记录可能会对你的协作工作流程造成潜在的灾难性后果。而且，不那么重要的是，rebase 丢失了合并提交提供的上下文——你看不到上游更改何时被合并到 feature 分支中。

## 资源

- [What is Version Control?](https://www.youtube.com/watch?v=Yc8sCSeMhi4)
- [Types of Version Control System](https://www.youtube.com/watch?v=kr62e_n6QuQ)
- [Git Tutorial for Beginners](https://www.youtube.com/watch?v=8JJ101D3knE&t=52s)
- [Git for Professionals Tutorial](https://www.youtube.com/watch?v=Uszj_k0DGsg)
- [Git and GitHub for Beginners - Crash Course](https://www.youtube.com/watch?v=RGOj5yH7evk&t=8s)
- [Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)
- [Git cheatsheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)
- [Exploring the Git command line – A getting started guide](https://veducate.co.uk/exploring-the-git-command-line/)


[第40天](day40.md) 见