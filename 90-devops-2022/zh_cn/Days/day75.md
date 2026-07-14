---
title: '#90DaysOfDevOps - GitHub Actions 概述 - 第75天'
published: false
description: 90DaysOfDevOps - GitHub Actions 概述
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049070
---
## GitHub Actions 概述

在本节中，我想继续探讨一种与刚才不同的方法。本次 session 我们将聚焦于 GitHub Actions。

GitHub Actions 是一个 CI/CD 平台，它允许我们构建、测试和部署我们的流水线，以及其他任务。它具有工作流（workflow）的概念，可以针对 GitHub 仓库进行构建和测试。你也可以使用 GitHub Actions 来驱动基于仓库中发生的其他事件的工作流。

### 工作流（Workflows）

总体而言，在 GitHub Actions 中，我们的任务被称为**工作流（Workflow）**。

- **工作流**是可配置的自动化流程。
- 以 YAML 文件形式定义。
- 包含并运行一个或多个**任务（jobs）**
- 当仓库中发生**事件（event）**时触发运行，或可以手动运行
- 每个仓库可以有多个工作流
- 一个**工作流**包含一个**任务**，然后包含完成该**任务**的**步骤（steps）**
- 在我们的**工作流**中，还会有一个**运行器（runner）**，我们的**工作流**在其上运行。

例如，你可以有一个**工作流**来构建和测试拉取请求（pull requests），另一个**工作流**在每次创建发布（release）时部署你的应用，还可以有另一个**工作流**在每次有人打开新 issue 时添加标签。

### 事件（Events）

事件是仓库中触发工作流运行的特定事件。

### 任务（Jobs）

任务是在运行器上执行的工作流中的一组步骤。

### 步骤（Steps）

任务中的每个步骤可以是一个被执行的 shell 脚本，或一个 action（动作）。步骤按顺序执行，并且相互依赖。

### 动作（Actions）

一种可重复使用的自定义应用程序，用于频繁重复的任务。

### 运行器（Runners）

运行器是运行工作流的服务器，每个运行器一次只运行一个任务。GitHub Actions 提供了运行 Ubuntu Linux、Microsoft Windows 和 macOS 运行器的能力。你也可以在特定的操作系统或硬件上托管自己的运行器。

你可以在下方看到这是如何运作的：我们的事件触发工作流 > 我们的工作流由两个任务组成 > 在我们的任务中，我们有步骤，然后有动作。

![](Images/Day75_CICD1.png)

### YAML

在我们开始实际用例之前，让我们快速看一下将上图转化为一个示例 YAML 文件。

我添加了 # 注释，标注了 YAML 工作流中各个组件所在的位置。

```
#Workflow
name: 90DaysOfDevOps
#Event
on: [push]
#Jobs
jobs:
  check-bats-version:
    #Runners
    runs-on: ubuntu-latest
    #Steps
    steps:
        #Actions
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

### GitHub Actions 动手实践

我认为 GitHub Actions 有很多选项，是的，它可以满足你在构建、测试、部署代码以及后续持续步骤方面的 CI/CD 需求。

我看到了很多选项和其他可以使用 GitHub Actions 实现的自动化任务。

### 使用 GitHub Actions 进行代码检查（Linting）

其中一个选项是确保你仓库中的代码干净整洁。这将是我们的第一个示例演示。

我将使用本节资源中链接的一些示例代码，我们将使用 `github/super-linter` 来检查我们的代码。

```
name: Super-Linter

on: push

jobs:
  super-lint:
    name: Lint code base
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run Super-Linter
        uses: github/super-linter@v3
        env:
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**github/super-linter**
你可以从上方看到，在我们的某个步骤中，有一个名为 github/super-linter 的 action，它引用了一个已经由社区编写的步骤。你可以在这里了解更多信息 [Super-Linter](https://github.com/github/super-linter)

"This repository is for the GitHub Action to run a Super-Linter. It is a simple combination of various linters, written in bash, to help validate your source code."

另外，在上面的代码片段中提到了 GITHUB_TOKEN，所以我很好奇这是做什么用的，以及为什么需要它。

"NOTE: If you pass the Environment variable `GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}` in your workflow, then the GitHub Super-Linter will mark the status of each individual linter run in the Checks section of a pull request. Without this you will only see the overall status of the full run. **There is no need to set the GitHub Secret as it is automatically set by GitHub, it only needs to be passed to the action.**"

加粗的文字在这个阶段值得注意。我们正在使用它，但我们不需要在仓库中设置任何环境变量。

我们将使用 Jenkins 演示中用到的仓库来测试。[Jenkins-HelloWorld](https://github.com/MichaelCade/Jenkins-HelloWorld)

这是我们 Jenkins session 结束时的仓库状态。

![](Images/Day75_CICD2.png)

为了充分利用 GitHub Actions，我们必须使用上方的 Actions 标签页从 marketplace 中选择，我稍后会介绍，或者我们可以使用上面的 super-linter 代码创建自己的文件。要创建自己的文件，你必须在仓库的这个确切位置创建一个新文件：`.github/workflows/workflow_name`，显然 workflow_name 应该是对你有用的名称，在这里我们可以有许多不同的工作流，针对我们的仓库执行不同的任务。

我们将创建 `.github/workflows/super-linter.yml`

![](Images/Day75_CICD3.png)

然后我们可以粘贴代码并提交到仓库，如果我们前往 Actions 标签页，现在会看到我们的 Super-Linter 工作流已列出，如下所示：

![](Images/Day75_CICD4.png)

我们在代码中定义了，当我们向仓库推送任何内容时，这个工作流就会运行，所以在将 super-linter.yml 推送到仓库时，我们触发了工作流。

![](Images/Day75_CICD5.png)

正如你从上方看到的，有一些错误，很可能是我的"黑客"能力 vs 编码能力的问题。

虽然实际上还不算是我的代码，至少现在还不是，在运行这个并得到错误时，我发现了这个 [issue](https://github.com/github/super-linter/issues/2255)

第二次尝试，我将 Super-Linter 的版本从 3 改到了 4，然后再次运行任务。

![](Images/Day75_CICD6.png)

如预期的那样，我的"黑客"编码带来了一些问题，你可以在这里的 [workflow](https://github.com/MichaelCade/Jenkins-HelloWorld/runs/5600278515?check_suite_focus=true) 中看到它们。

我想展示一下，当工作流中的某些内容失败或报告错误时，我们仓库现在的样子。

![](Images/Day75_CICD7.png)

现在，如果我们解决代码中的问题并推送更改，我们的工作流将再次运行（你可以从图片中看到，我花了一段时间才解决我们的 "bug"）。删除文件可能不是推荐的做法，但这是一个快速展示问题被解决的方法。

![](Images/Day75_CICD8.png)

如果你点击上方高亮的 "new workflow" 按钮，这将打开通往大量 actions 的大门。在本挑战中你可能已经注意到一件事，就是我们不想重复造轮子，我们想站在巨人的肩膀上，广泛分享我们的代码、自动化和技能，让我们的生活更轻松。

![](Images/Day75_CICD9.png)

哦，我还没给你看工作流成功时仓库上的绿色对勾呢。

![](Images/Day75_CICD10.png)

我认为这从基础角度涵盖了 GitHub Actions 的内容，但如果你和我一样，你可能正在思考 GitHub Actions 还能如何用于自动化大量任务。

接下来我们将涵盖 CD 的另一个领域，我们将研究 ArgoCD，将我们的应用部署到各个环境中。

## 资源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

[第76天](day76.md)见。
