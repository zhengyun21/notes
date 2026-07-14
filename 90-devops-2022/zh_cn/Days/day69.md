---
title: '#90DaysOfDevOps - Ansible 的其他内容 - Automation Controller（自动化控制器）、AWX、Vault - 第69天'
published: false
description: '90DaysOfDevOps - Ansible 的其他内容 - Automation Controller（自动化控制器）、AWX、Vault'
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048714
---
## Ansible 的其他内容 - Automation Controller（自动化控制器）、AWX、Vault

在结束配置管理这一节时，我想了解一下在处理 Ansible 时你可能会遇到的其他领域。

Ansible 自动化平台由许多产品组成。

Red Hat Ansible Automation Platform 是构建和运营整个组织自动化基础的平台。该平台包含实施企业级自动化所需的所有工具。

![](Images/Day69_config1.png)

我将尝试在这篇文章中涵盖其中一些内容。但如需更多信息，官方的 Red Hat Ansible 网站将提供更多资料。[Ansible.com](https://www.ansible.com/?hsLang=en-us)

### Ansible Automation Controller | AWX

我将这两者捆绑在一起介绍，因为 Automation Controller 和 AWX 在提供的功能上非常相似。

AWX 项目（简称 AWX）是一个开源社区项目，由 Red Hat 赞助，使你能够更好地在环境中控制你的 Ansible 项目。AWX 是 Automation Controller 组件的上游项目。

如果你正在寻找企业级解决方案，那么你会寻找 Automation Controller，或者你可能之前听说过它叫 Ansible Tower。Ansible Automation Controller 是 Ansible Automation Platform 的控制平面。

AWX 和 Automation Controller 都带来了以下功能，超越了我们在本节中迄今为止涵盖的所有内容。

- 用户界面（User Interface）
- 基于角色的访问控制（Role Based Access Control）
- 工作流（Workflows）
- CI/CD 集成

Automation Controller 是企业级产品，你需要为其支持付费。

我们将研究如何在 minikube Kubernetes 环境中部署 AWX。

### 部署 Ansible AWX

AWX 不一定需要部署到 Kubernetes 集群，AWX 的 [github](https://github.com/ansible/awx) 会给你详细信息。然而，从 18.0 版本开始，AWX Operator 是安装 AWX 的首选方式。

首先，我们需要一个 minikube 集群。如果你在 Kubernetes 章节中跟进了，我们可以通过 `minikube start --cpus=4 --memory=6g --addons=ingress` 命令创建一个新的 minikube 集群。

![](Images/Day69_config2.png)

官方的 [Ansible AWX Operator](https://github.com/ansible/awx-operator) 可以在这里找到。正如安装说明中所述，你应该克隆这个仓库，然后运行部署。

我 fork 了上面的仓库，然后运行了 `git clone https://github.com/MichaelCade/awx-operator.git`，我的建议是你也这样做，不要使用我的仓库，因为我可能会更改内容或者它可能已经不存在了。

在克隆的仓库中，你会找到一个 awx-demo.yml 文件，我们需要将 `NodePort` 更改为 `ClusterIP`，如下所示：

```
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: ClusterIP
```

下一步是定义我们将要部署 awx operator 的命名空间，使用 `export NAMESPACE=awx` 命令，然后运行 `make deploy`，我们将开始部署。

![](Images/Day69_config3.png)

检查我们是否有新的命名空间，以及我们的 awx-operator-controller pod 是否在我们的命名空间中运行。`kubectl get pods -n awx`

![](Images/Day69_config4.png)

在克隆的仓库中，你会找到一个名为 awx-demo.yml 的文件，我们现在想要将它部署到我们的 Kubernetes 集群和 awx 命名空间中。`kubectl create -f awx-demo.yml -n awx`

![](Images/Day69_config5.png)

你可以使用 `kubectl get pods -n awx -w` 来密切关注进度，它将实时监视正在发生的事情。

当一切运行时，你应该会看到类似下图的内容。

![](Images/Day69_config6.png)

现在，在一个新终端中运行 `minikube service awx-demo-service --url -n $NAMESPACE` 后，我们应该能够通过 minikube ingress 访问我们的 awx 部署。

![](Images/Day69_config7.png)

如果我们然后打开浏览器访问该地址 []，你可以看到系统提示我们输入用户名和密码。

![](Images/Day69_config8.png)

默认用户名是 admin，要获取密码，我们可以运行以下命令：`kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" -n awx| base64 --decode`

![](Images/Day69_config9.png)

显然，这为你提供了一个 UI，可以在一个中心位置管理你的 playbook 和配置管理任务，它还允许你作为团队一起工作，而不是像我们在本节中迄今为止一直在做的那样，从单个 ansible 控制站运行。

这是另一个你可以花更多时间深入了解该工具功能的领域。

我要推荐 Jeff Geerling 的一个很棒的资源，它更详细地介绍了如何使用 Ansible AWX。[Ansible 101 - Episode 10 - Ansible Tower and AWX](https://www.youtube.com/watch?v=iKmY4jEiy_A&t=752s)

在这个视频中，他还详细介绍了 Automation Controller（以前叫 Ansible Tower）和 Ansible AWX（免费且开源）之间的区别。

### Ansible Vault

`ansible-vault` 允许我们加密和解密 Ansible 数据文件。在本节中，我们一直在回避这个问题，并将一些敏感信息以明文形式存放。

Ansible 二进制文件中内置了 `ansible-vault`，它允许我们掩盖这些敏感信息。

![](Images/Day69_config10.png)

Secrets Management（机密管理）已经逐渐成为另一个应该投入更多时间的领域，除了 HashiCorp Vault 或 AWS Key Management Service 等工具之外。我将把这一点标记为一个需要深入研究的领域。

我要再次链接 Jeff Geerling 的一个很棒的资源和演示。[Ansible 101 - Episode 6 - Ansible Vault and Roles](https://www.youtube.com/watch?v=JFweg2dUvqM)

### Ansible Galaxy（文档）

现在，我们已经使用 `ansible-galaxy` 为我们的演示项目创建了一些 roles 和文件结构。但我们还有 [Ansible Galaxy documentation](https://galaxy.ansible.com/docs/)

"Galaxy 是一个查找和共享 Ansible 内容的中心。"

### Ansible Testing

- [Ansible Molecule](https://molecule.readthedocs.io/en/latest/) - Molecule 项目旨在帮助开发和测试 Ansible roles

- [Ansible Lint](https://ansible-lint.readthedocs.io/en/latest/) - 用于 lint 检查 playbooks、roles 和 collections 的 CLI 工具

### 其他资源

- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)

## 相关资源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最后一个播放列表是本节大部分代码和想法的来源，是一个非常棒的资源，以视频格式进行讲解。

这篇文章结束了我们对配置管理的探讨，接下来我们将进入 CI/CD Pipelines（CI/CD 流水线）以及一些我们可能在外看到和使用的工具与流程，以实现应用程序开发和发布的这一工作流。

[第70天](day70.md)见。
