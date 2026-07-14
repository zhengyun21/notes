---
title: '#90DaysOfDevOps - Jenkins 动手实践 - 第72天'
published: false
description: 90DaysOfDevOps - Jenkins 动手实践
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048829
---
## Jenkins 动手实践

今天的计划是动手实践 Jenkins，作为我们 CI 流水线的一部分让它做点什么，看看我们可以使用的一些示例代码库。

### 什么是流水线（pipeline）？

在我们开始之前，我们需要知道在 CI 中什么是流水线，我们在昨天的 session 中已经用下图介绍过了。

![](Images/Day71_CICD4.png)

我们想把上面的流程或步骤自动化，最终得到一个已部署的应用程序，然后我们可以将其交付给客户、最终用户等。

这个自动化流程使我们能够对用户和客户进行版本控制。每一次变更、功能增强、bug 修复等都经过这个自动化流程，确认一切正常，而无需太多人工干预来确保我们的代码是好的。

这个过程涉及到以可靠且可重复的方式构建软件，以及将构建好的软件（称为 "build"）推进到多个测试和部署阶段。

Jenkins pipeline（Jenkins 流水线）被写入一个名为 Jenkinsfile 的文本文件中。Jenkinsfile 本身应该被提交到源代码控制仓库中。这也被称为 Pipeline as code（流水线即代码），我们也可以将其与几周前介绍过的 Infrastructure as code（基础设施即代码）相提并论。

[Jenkins Pipeline 定义](https://www.jenkins.io/doc/book/pipeline/#ji-toolbar)

### 部署 Jenkins

我部署 Jenkins 时遇到了一些有趣的问题。你会从 [文档](https://www.jenkins.io/doc/book/installing/) 中注意到，有很多选项可以安装 Jenkins。

鉴于我手头有 minikube，而且我们已经多次使用过它，所以我也想用它来完成这个任务。（而且它是免费的！）尽管 [Kubernetes 安装](https://www.jenkins.io/doc/book/installing/kubernetes/) 中的步骤让我碰壁了，无法让东西运行起来，你可以在这里对比我记录的步骤和官方文档的步骤。

第一步是启动我们的 minikube 集群，我们可以简单地使用 `minikube start` 命令来做到这一点。

![](Images/Day72_CICD1.png)

我添加了一个文件夹，里面包含了所有的 YAML 配置和值，可以在 [这里](days/CICD/Jenkins) 找到。现在我们有了集群，我们可以运行以下命令来创建我们的 jenkins 命名空间。`kubectl create -f jenkins-namespace.yml`

![](Images/Day72_CICD2.png)

我们将使用 Helm 将 jenkins 部署到我们的集群中，我们在 Kubernetes 章节中介绍过 Helm。我们首先需要添加 jenkinsci helm 仓库 `helm repo add jenkinsci https://charts.jenkins.io`，然后更新我们的 charts `helm repo update`。

![](Images/Day72_CICD3.png)

Jenkins 的理念是它会保存其流水线的状态，你可以运行上述 helm 安装而不使用持久化存储，但如果这些 pod 被重启、更改或修改，那么你做的任何流水线或配置都会丢失。我们将使用 jenkins-volume.yml 文件和 `kubectl apply -f jenkins-volume.yml` 命令创建一个用于持久化的卷。

![](Images/Day72_CICD4.png)

我们还需要一个 service account（服务账户），我们可以使用这个 yaml 文件和命令来创建。`kubectl apply -f jenkins-sa.yml`

![](Images/Day72_CICD5.png)

在这个阶段，我们准备好使用 helm chart 进行部署了。我们首先用 `chart=jenkinsci/jenkins` 定义我们的 chart，然后使用这个命令进行部署，其中 jenkins-values.yml 包含了我们之前部署到集群的持久化存储和服务账户配置。`helm install jenkins -n jenkins -f jenkins-values.yml $chart`

![](Images/Day72_CICD6.png)

在这个阶段，我们的 pod 将正在拉取镜像，但 pod 将无法访问存储，因此无法开始 Jenkins 的启动和配置。

这就是文档没有很好地帮助我理解需要发生什么的地方。但我们可以看到，我们没有权限启动我们的 jenkins 安装。

![](Images/Day72_CICD7.png)

为了解决上述问题，我们需要确保提供访问权限或正确的权限，以便我们的 jenkins pod 能够写入我们指定的位置。我们可以通过 `minikube ssh` 进入我们正在运行的 minikube docker 容器，然后使用 `sudo chown -R 1000:1000 /data/jenkins-volume` 确保我们的数据卷上设置了权限。

![](Images/Day72_CICD8.png)

上述过程应该可以修复 pod 的问题，但如果不行，你可以用 `kubectl delete pod jenkins-0 -n jenkins` 命令强制刷新 pod。此时你应该有 2/2 个名为 jenkins-0 的运行中 pod。

![](Images/Day72_CICD9.png)

我们现在需要我们的 admin 密码，我们可以使用以下命令获取。`kubectl exec --namespace jenkins -it svc/jenkins -c jenkins -- /bin/cat /run/secrets/chart-admin-password && echo`

![](Images/Day72_CICD10.png)

现在打开一个新终端，因为我们要使用 `port-forward` 命令来允许我们从工作站访问。`kubectl --namespace jenkins port-forward svc/jenkins 8080:8080`

![](Images/Day72_CICD11.png)

我们现在应该能够打开浏览器并登录到 http://localhost:8080，使用用户名：admin 和我们在前面步骤中获取的密码进行认证。

![](Images/Day72_CICD12.png)

认证后，我们的 Jenkins 欢迎页面应该看起来像这样：

![](Images/Day72_CICD13.png)

从这里开始，我建议前往 "Manage Jenkins"，你会看到 "Manage Plugins"，其中会有一些可用的更新。选择所有这些插件并选择 "Download now and install after restart"

![](Images/Day72_CICD14.png)

如果你想更进一步，使用 shell 脚本自动化部署 Jenkins，这个很棒的仓库是在 Twitter 上分享给我的 [mehyedes/nodejs-k8s](https://github.com/mehyedes/nodejs-k8s/blob/main/docs/automated-setup.md)


### Jenkinsfile
现在我们的 Jenkins 已经部署在 Kubernetes 集群中了，我们可以回过头来思考这个 Jenkinsfile。

每个 Jenkinsfile 都可能像这样开头，首先是定义流水线的步骤，在这个实例中你有 Build（构建）> Test（测试）> Deploy（部署）。但我们除了使用 `echo` 命令来调用特定阶段外，实际上并没有做什么。

```

Jenkinsfile (Declarative Pipeline)

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}

```
在我们的 Jenkins 仪表板中，选择 "New Item"，给项目起一个名字，我将它命名为 "echo1"，我建议这是一个 Pipeline（流水线）。

![](Images/Day72_CICD15.png)

点击 Ok，然后你将有标签页（General、Build Triggers、Advanced Project Options 和 Pipeline）。对于一个简单的测试，我们只关心 Pipeline。在 Pipeline 下，你有能力添加一个脚本，我们可以将上面的脚本复制粘贴到框中。

正如我们上面所说，这不会做太多事情，但它会向我们展示 Build > Test > Deploy 的各个阶段。

![](Images/Day72_CICD16.png)

点击 Save，我们现在可以使用下面高亮的 "Build Now" 来运行我们的构建。

![](Images/Day72_CICD17.png)

我们还应该打开一个终端并运行 `kubectl get pods -n jenkins` 来查看那里发生了什么。

![](Images/Day72_CICD18.png)

好了，非常简单的东西，但我们现在可以看到我们的 Jenkins 部署和安装正在正确运行，我们可以开始在这里看到 CI 流水线的构建块了。

在下一节中，我们将构建一个 Jenkins Pipeline。

## 相关资源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFY4jEiy_A)

[第73天](day73.md)见。
