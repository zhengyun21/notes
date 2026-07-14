---
title: '#90DaysOfDevOps - Hello World - Jenkinsfile 应用流水线 - 第74天'
published: false
description: 90DaysOfDevOps - Hello World - Jenkinsfile 应用流水线
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048744
---
## Hello World - Jenkinsfile 应用流水线

在上一节中，我们在 Jenkins 中构建了一个简单的流水线，它将 GitHub 公开仓库中 Dockerfile 构建的 Docker 镜像推送到我们的私有 DockerHub 仓库。

在这一节中，我们想更进一步，通过我们的简单应用实现以下目标。

### 目标

- Dockerfile (Hello World)
- Jenkinsfile
- 当 GitHub 仓库更新时触发 Jenkins 流水线
- 使用 GitHub 仓库作为源代码。
- 运行 - 克隆/获取仓库、构建、测试、部署阶段
- 以递增版本号部署到 DockerHub
- 延伸目标：部署到我们的 Kubernetes 集群（这将涉及另一个任务，以及使用 GitHub 凭据的 manifest 仓库）

### 第一步

我们有我们的 [GitHub 仓库](https://github.com/MichaelCade/Jenkins-HelloWorld)，目前包含我们的 Dockerfile 和 index.html。

![](Images/Day74_CICD1.png)

基于以上内容，这是我们之前在流水线中使用的源代码，现在我们希望将 Jenkins 流水线脚本也添加到我们的 GitHub 仓库中。

![](Images/Day74_CICD2.png)

现在回到我们的 Jenkins 仪表板，我们将创建一个新的流水线，但这一次我们不再粘贴脚本，而是使用 "Pipeline script from SCM"。然后我们将使用下方的配置选项。

作为参考，我们将使用 https://github.com/MichaelCade/Jenkins-HelloWorld.git 作为仓库 URL。

![](Images/Day74_CICD3.png)

到这一步，我们可以点击保存和应用，然后就能够手动运行我们的流水线，构建新的 Docker 镜像并上传到我们的 DockerHub 仓库。

然而，我还想确保我们设置一个调度计划，每当我们仓库或源代码发生变更时，就触发一次构建。我们可以使用 webhooks（网络钩子），也可以使用定时拉取（scheduled pull）。

这是一个重要的考虑因素，因为如果你使用昂贵的云资源来托管你的流水线，并且你的代码仓库有很多变更，那么你将产生大量成本。我们知道这是一个演示环境，这就是为什么我使用 "poll scm" 选项。（另外，我认为使用 minikube 时我无法使用 webhooks）

![](Images/Day74_CICD4.png)

自昨天的 session 以来，我做了一个改动，我想将镜像上传到一个公开仓库，在这种情况下是 michaelcade1\90DaysOfDevOps，我的 Jenkinsfile 已经有这个更改。并且从前面的章节中，我已经移除了所有现有的演示容器镜像。

![](Images/Day74_CICD5.png)

回溯一下，我们创建了流水线，然后按照之前展示的方式添加了配置。

![](Images/Day74_CICD6.png)

在这个阶段，我们的流水线从未运行过，你的阶段视图（stage view）看起来会是这样的。

![](Images/Day74_CICD7.png)

现在让我们点击 "Build Now" 按钮，我们的阶段视图将显示各个阶段。

![](Images/Day74_CICD8.png)

如果我们前往 DockerHub 仓库查看，应该会有 2 个新的 Docker 镜像。我们应该有一个构建 ID 为 1 的镜像，以及一个 latest 标签的镜像，因为我们每次基于 "Upload to DockerHub" 创建构建时，都会使用 Jenkins Build_ID 环境变量发送一个版本号，同时还会发布一个 latest 标签。

![](Images/Day74_CICD9.png)

让我们去 GitHub 仓库中更新我们的 index.html 文件，如下所示，至于第 1 版的 index.html 说了什么，就留给你去发现了。

![](Images/Day74_CICD10.png)

如果我们回到 Jenkins 并再次选择 "Build Now"。我们会看到我们的 #2 构建成功了。

![](Images/Day74_CICD11.png)

然后快速查看一下 DockerHub，我们可以看到带有版本 2 标签的镜像，以及我们的 latest 标签。

![](Images/Day74_CICD12.png)

这里值得一提的是，我已经在我的 Kubernetes 集群中添加了一个 secret，使我有权限验证并将 docker 构建推送到 DockerHub。如果你正在跟着操作，你应该为你的账户重复这个过程，同时修改与我的仓库和账户关联的 Jenkinsfile。

## 资源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

[第75天](day75.md)见。
