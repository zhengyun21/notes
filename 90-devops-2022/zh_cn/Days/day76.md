---
title: '#90DaysOfDevOps - ArgoCD 概述 - 第76天'
published: false
description: 90DaysOfDevOps - ArgoCD 概述
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048809
---
## ArgoCD 概述

"Argo CD is a declarative, GitOps continuous delivery tool for Kubernetes"

版本控制是关键，你是否曾经对环境进行过临时更改，却完全不记得那个更改，而且因为一切正常、灯都是绿的，你就继续埋头前行？你是否曾经做过更改然后搞砸了一切或部分东西？你可能知道是你做的更改，你可以快速回滚你的更改、那个糟糕的脚本或拼写错误。那么，你是否曾经在大规模情况下这样做过，也许不是你做的，或者没有立即被发现，现在业务正在受到影响。因此，版本控制很重要。不仅如此，"Application definitions, configurations, and environments should be declarative, and version controlled." 除此之外（这来自 ArgoCD），他们还提到 "Application deployment and lifecycle management should be automated, auditable, and easy to understand."

从运维（Operations）背景出发，但在基础设施即代码（Infrastructure as Code）方面进行了大量实践，这是确保所有这些好东西在持续部署/交付工作流中得到妥善处理的下一步。

[什么是 ArgoCD](https://argo-cd.readthedocs.io/en/stable/)

### 部署 ArgoCD

我们将再次在本地使用我们值得信赖的 minikube Kubernetes 集群进行此次部署。

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

![](Images/Day76_CICD1.png)

使用 `kubectl get pods -n argocd` 确保所有 ArgoCD pod 都在运行。

![](Images/Day76_CICD2.png)

另外，让我们用 `kubectl get all -n argocd` 查看我们在命名空间中部署的所有内容。

![](Images/Day76_CICD3.png)

当上方看起来没问题时，我们应该考虑通过端口转发（port forward）来访问它。使用 `kubectl port-forward svc/argocd-server -n argocd 8080:443` 命令。在一个新的终端中执行此操作。

然后打开一个新的 web 浏览器，访问 https://localhost:8080

![](Images/Day76_CICD4.png)

要登录，你需要用户名 admin，然后使用 `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo` 获取你创建的 secret 作为密码。

![](Images/Day76_CICD5.png)

登录后，你将拥有空白的 CD 画布。

![](Images/Day76_CICD6.png)

### 部署我们的应用

现在 ArgoCD 已经启动并运行，我们可以开始使用它从 Git 仓库以及 Helm 部署我们的应用。

我想部署的应用是 Pac-Man，没错，就是那个著名的游戏，也是我在数据管理演示中经常使用的，这不会是我们最后一次看到 Pac-Man。

你可以在这里找到 [Pac-Man](https://github.com/MichaelCade/pacman-tanzu.git) 的仓库。

与其使用截图一步步讲解，我认为创建一个 walkthrough 视频来涵盖这个特定应用部署的步骤会更容易。

[ArgoCD Demo - 90DaysOfDevOps](https://www.youtube.com/watch?v=w6J413_j0hA)

注意 - 在视频中，有一个服务始终无法满足应用健康（app health）为健康状态的要求，这是因为 pacman 服务的 LoadBalancer 类型处于 pending 状态，在 Minikube 中我们没有配置负载均衡器（loadbalancer）。如果你想测试这个，你可以将服务的 YAML 改为 ClusterIP 并使用端口转发来玩游戏。

这结束了 CI/CD 流水线部分，我觉得目前业界对这一领域非常关注，你还会听到 GitOps 相关的术语，这通常与 CI/CD 中使用的方法论有关。

下一部分我们将进入可观测性（Observability），这是另一个并不新颖但越来越重要的概念或领域，因为我们会以不同的方式看待我们的环境。

## 资源

- [Jenkins is the way to build, test, deploy](https://youtu.be/_MXtbjwsz3A)
- [Jenkins.io](https://www.jenkins.io/)
- [ArgoCD](https://argo-cd.readthedocs.io/en/stable/)
- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)
- [What is Jenkins?](https://www.youtube.com/watch?v=LFDrDnKPOTg)
- [Complete Jenkins Tutorial](https://www.youtube.com/watch?v=nCKxl7Q_20I&t=3s)
- [GitHub Actions](https://www.youtube.com/watch?v=R8_veQiYBjI)
- [GitHub Actions CI/CD](https://www.youtube.com/watch?v=mFFXuXjVgkU)

[第77天](day77.md)见。
