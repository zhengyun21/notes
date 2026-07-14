---
title: '#90DaysOfDevOps - 构建 Jenkins 流水线 - 第73天'
published: false
description: 90DaysOfDevOps - 构建 Jenkins 流水线
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048766
---
## 构建 Jenkins 流水线

在上一节中，我们将 Jenkins 部署到了 Minikube 集群，并设置了一个非常基础的 Jenkins 流水线，除了输出流水线的各个阶段外，几乎没有做其他事情。

你可能还注意到，在创建 Jenkins 流水线时有一些示例脚本可供我们使用。

![](Images/Day73_CICD1.png)

第一个演示脚本是 "Declartive (Kubernetes)"，你可以在下方看到各个阶段。

```
// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: ubuntu
    command:
    - sleep
    args:
    - infinity
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            defaultContainer 'shell'
        }
    }
    stages {
        stage('Main') {
            steps {
                sh 'hostname'
            }
        }
    }
}
```
你可以在下方看到运行该流水线后的结果。

![](Images/Day73_CICD2.png)

### 创建任务

**目标**

- 创建一个简单的应用，并存放在 GitHub 公开仓库中 (https://github.com/scriptcamp/kubernetes-kaniko.git)

- 使用 Jenkins 构建我们的 Docker 容器镜像，并推送到 Docker Hub。（为此我们将使用一个私有仓库）

要在我们的 Minikube 集群中实现这一目标，我们需要使用一种名为 [Kaniko](https://github.com/GoogleContainerTools/kaniko#running-kaniko-in-a-kubernetes-cluster) 的工具。不过一般来说，如果你在真实的 Kubernetes 集群中使用 Jenkins，或者将其运行在服务器上，那么你可以指定一个代理（agent），从而获得执行 docker build 命令并将镜像上传到 DockerHub 的能力。

考虑到以上情况，我们还将在 Kubernetes 中部署一个包含我们 GitHub 凭据的 secret。

```
kubectl create secret docker-registry dockercred \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<dockerhub-username> \
    --docker-password=<dockerhub-password>\
    --docker-email=<dockerhub-email>
```

事实上，我想分享另一个来自 [DevOpsCube.com](https://devopscube.com/build-docker-image-kubernetes-pod/) 的优秀资源，它详细介绍了我们即将涵盖的大部分内容。

### 在 Jenkins 中添加凭据

然而，如果你使用的是与我们不同的 Jenkins 系统，那么你可能会希望在 Jenkins 中定义你的凭据，然后在多个流水线和配置中重复使用它们。我们可以在流水线中通过创建时确定的 ID 来引用这些凭据。我提前创建了一个 DockerHub 和 GitHub 的用户条目。

首先选择 "Manage Jenkins"，然后选择 "Manage Credentials"

![](Images/Day73_CICD3.png)

你会在页面中央看到 "Stores scoped to Jenkins"，点击这里的 Jenkins。

![](Images/Day73_CICD4.png)

现在选择 "Global Credentials (Unrestricted)"

![](Images/Day73_CICD5.png)

然后在左上角你会看到 "Add Credentials"

![](Images/Day73_CICD6.png)

填写你账户的详细信息，然后选择 OK，记住 ID 是你在调用此凭据时需要引用的内容。我这里的建议是，使用特定的令牌访问（token access）而不是密码。

![](Images/Day73_CICD7.png)

对于 GitHub，你应该使用 [Personal Access Token](https://vzilla.co.uk/vzilla-blog/creating-updating-your-github-personal-access-token)

就我个人而言，我发现这个创建过程并不是非常直观，所以即使我们没有使用它，我也想分享这个过程，因为从 UI 上看并不清楚。

### 构建流水线

我们已经将 DockerHub 凭据作为 secret 部署到了 Kubernetes 集群中，我们将在流水线的 docker 部署到 DockerHub 阶段调用它。

你可以在下方看到流水线脚本，这可以进一步变成我们 GitHub 仓库中的 Jenkinsfile，你也可以在流水线的 "Get the project" 阶段看到列出的仓库。

```
podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Get the project') {
      git url: 'https://github.com/scriptcamp/kubernetes-kaniko.git', branch: 'main'
      container('maven') {
        stage('Test the project') {
          sh '''
          echo pwd
          '''
        }
      }
    }

    stage('Build & Test the Docker Image') {
      container('kaniko') {
        stage('Deploy to DockerHub') {
          sh '''
            /kaniko/executor --context `pwd` --destination michaelcade1/helloworld:latest
          '''
        }
      }
    }

  }
}
```

要在 Jenkins 仪表板上开始操作，我们需要选择 "New Item"

![](Images/Day73_CICD8.png)

然后我们将为任务命名，选择 Pipeline，然后点击 ok。

![](Images/Day73_CICD9.png)

我们不会选择任何通用（General）或构建触发器（Build Triggers）选项，但你可以尝试这些选项，因为有一些有趣的调度计划和其他配置可能会很有用。

![](Images/Day73_CICD10.png)

我们只对最后的 Pipeline 标签页感兴趣。

![](Images/Day73_CICD11.png)

在 Pipeline 定义中，我们将上面复制的流水线脚本粘贴到 Script 部分，然后点击保存。

![](Images/Day73_CICD12.png)

接下来我们将在页面左侧选择 "Build Now" 选项。

![](Images/Day73_CICD13.png)

你现在应该等待很短的一段时间，真的不到一分钟。然后你应该会在状态（status）下方看到我们在脚本中定义的阶段。

![](Images/Day73_CICD14.png)

更重要的是，如果我们现在前往 DockerHub 查看，就会发现有了新的构建。

![](Images/Day73_CICD15.png)

这整个过程确实花了一段时间才弄清楚，但我想要坚持下去，目的是为了亲自动手实践，并完成一个任何人都可以使用 minikube 以及访问 GitHub 和 DockerHub 来运行的场景。

这次演示使用的 DockerHub 仓库是私有的。但在下一节中，我想推进其中的一些阶段，让它们实际做一些事情，而不是只打印 `pwd`，而是真正运行一些测试和构建阶段。

## 资源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

[第74天](day74.md)见。
