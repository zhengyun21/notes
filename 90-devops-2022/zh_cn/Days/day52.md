---
title: '#90DaysOfDevOps - 搭建多节点 Kubernetes 集群 - 第52天'
published: false
description: 90DaysOfDevOps - 搭建多节点 Kubernetes 集群
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049050
---
## 搭建多节点 Kubernetes 集群

我本来想把标题写成"使用 Vagrant 搭建多节点 Kubernetes 集群"，但觉得可能太长了！

在昨天的课程中，我们使用了一个很酷的项目来部署我们的第一个 Kubernetes 集群，并稍微实践了一下使用 Kubernetes 时你会遇到的最重要的 CLI 工具（kubectl）。

在这里，我们将使用 VirtualBox 作为基础，但正如上次我们在 Linux 部分谈到 Vagrant 时提到的，我们实际上可以使用任何支持的管理程序或虚拟化工具。那是在 [第14天](day14.md)，我们在 Linux 部分部署了一台 Ubuntu 机器。

### Vagrant 快速回顾

Vagrant 是一个管理虚拟机生命周期的 CLI 工具。我们可以使用 vagrant 在许多不同的平台上启动和关闭虚拟机，包括 vSphere、Hyper-v、Virtual Box 以及 Docker。它确实还有其他提供商，但我们就坚持使用 Virtual Box，所以我们可以开始了。

我将使用这个[博客和仓库](https://devopscube.com/kubernetes-cluster-vagrant/)作为基线来介绍配置。不过我建议，如果这是你第一次部署 Kubernetes 集群，那么也许还要研究一下如何手动完成，这样至少你知道这看起来是什么样的。虽然我要说的是，这种第 0 天的运维工作随着 Kubernetes 的每个版本发布都在变得更高效。我非常怀念 VMware 和 ESX 的时代，那时你至少需要一天时间来部署 3 台 ESX 服务器，现在我们可以在一小时内启动并运行。就 Kubernetes 而言，我们正在朝着这个方向发展。

### Kubernetes 实验室环境

我已经在 [Kubernetes 文件夹](days/kubernetes) 中上传了我们将用于构建环境的 vagrantfile。获取这个文件，然后在你的终端中导航到这个目录。我再次使用 Windows，所以我将使用 PowerShell 来执行 vagrant 的工作站命令。如果你没有 vagrant，你可以使用 arkade，我们在昨天安装 minikube 和其他工具时介绍过这个。一个简单的命令 `arkade get vagrant` 应该可以让你下载并安装最新版本的 vagrant。

当你在目录中时，你可以简单地运行 `vagrant up`，如果所有配置都正确，你应该会看到以下终端内容开始启动。

![](Images/Day52_Kubernetes1.png)

在终端中，你会看到许多步骤正在进行，但与此同时，让我们看看我们实际上在构建什么。

![](Images/Day52_Kubernetes2.png)

从上面你可以看到，我们将构建 3 台虚拟机，我们将有一个控制平面节点和两个工作节点。如果你回到 [第49天](Days/day49.md)，你会看到图像中这些区域的更多描述。

此外，在图像中我们表明我们的 kubectl 访问将来自集群外部并命中 kube apiserver，但实际上，作为 vagrant 配置的一部分，我们正在每个节点上部署 kubectl，这样我们就可以从每个节点内部访问集群。

构建这个实验室的过程可能需要 5 分钟到 30 分钟，具体取决于你的设置。

我很快也会介绍这些脚本，但如果你查看 vagrant 文件，你会注意到我们在部署过程中调用了 3 个脚本，这实际上就是集群创建的地方。我们已经看到了使用 vagrant 部署我们的虚拟机和操作系统安装是多么容易，但能够在部署过程中运行 shell 脚本的能力使得自动化这些实验室构建变得相当有趣。

完成后，我们可以 ssh 到我们的某个节点，`vagrant ssh master` 应该从终端获得访问权限，默认用户名和密码是 `vagrant/vagrant`。

你也可以使用 `vagrant ssh node01` 和 `vagrant ssh node02` 来访问工作节点，如果你愿意的话。

![](Images/Day52_Kubernetes3.png)

现在我们进入了新集群中的上述某个节点，我们可以发出 `kubectl get nodes` 命令来显示我们的 3 节点集群及其状态。

![](Images/Day52_Kubernetes4.png)

此时，我们有一个正在运行的 3 节点集群，包含 1 个控制平面节点和 2 个工作节点。

### Vagrantfile 和 Shell 脚本演练

如果我们查看我们的 vagrantfile，你会看到我们正在定义多个工作节点、桥接网络的 IP 地址以及一些命名。你还会注意到，我们正在调用一些要在特定主机上运行的脚本。

```
NUM_WORKER_NODES=2
IP_NW="10.0.0."
IP_START=10

Vagrant.configure("2") do |config|
    config.vm.provision "shell", inline: <<-SHELL
        apt-get update -y
        echo "$IP_NW$((IP_START))  master-node" >> /etc/hosts
        echo "$IP_NW$((IP_START+1))  worker-node01" >> /etc/hosts
        echo "$IP_NW$((IP_START+2))  worker-node02" >> /etc/hosts
    SHELL
    config.vm.box = "bento/ubuntu-21.10"
    config.vm.box_check_update = true

    config.vm.define "master" do |master|
      master.vm.hostname = "master-node"
      master.vm.network "private_network", ip: IP_NW + "#{IP_START}"
      master.vm.provider "virtualbox" do |vb|
          vb.memory = 4048
          vb.cpus = 2
          vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      master.vm.provision "shell", path: "scripts/common.sh"
      master.vm.provision "shell", path: "scripts/master.sh"
    end

    (1..NUM_WORKER_NODES).each do |i|
      config.vm.define "node0#{i}" do |node|
        node.vm.hostname = "worker-node0#{i}"
        node.vm.network "private_network", ip: IP_NW + "#{IP_START + i}"
        node.vm.provider "virtualbox" do |vb|
            vb.memory = 2048
            vb.cpus = 1
            vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        end
        node.vm.provision "shell", path: "scripts/common.sh"
        node.vm.provision "shell", path: "scripts/node.sh"
      end
    end
  end
  ```

让我们分解一下正在运行的这些脚本。我们在上面的 VAGRANTFILE 中列出了三个要在特定节点上运行的脚本。

`master.vm.provision "shell", path: "scripts/common.sh"`

上面的这个脚本将专注于让节点准备就绪，它将在我们的所有 3 个节点上运行，它将移除任何现有的 Docker 组件并重新安装 Docker 和 ContainerD，以及 kubeadm、kubelet 和 kubectl。该脚本还会更新系统上的现有软件包。

`master.vm.provision "shell", path: "scripts/master.sh"`

master.sh 脚本将只在控制平面节点上运行，这个脚本将使用 kubeadm 命令创建 Kubernetes 集群。它还将准备访问此集群的配置上下文，我们接下来会介绍。

`node.vm.provision "shell", path: "scripts/node.sh"`

这将会获取由 master 创建的配置，并将我们的节点加入到 Kubernetes 集群中，这个加入过程同样使用 kubeadm 和另一个可以在 config 文件夹中找到的脚本。

### 访问 Kubernetes 集群

现在我们已经部署了两个集群，一个是我们在上一节部署的 minikube 集群，另一个是我们刚刚部署到 VirtualBox 的新 3 节点集群。

此外，在你运行 vagrant 的机器上也可以访问该配置文件，其中包含我们如何从工作站访问集群的信息。

在我们展示之前，让我先谈谈上下文（context）。

![](Images/Day52_Kubernetes5.png)

上下文很重要，从你的台式机或笔记本电脑访问你的 Kubernetes 集群是必需的。有很多不同的选择，人们显然使用不同的操作系统作为他们的日常使用系统。

默认情况下，Kubernetes CLI 客户端（kubectl）使用 `C:\Users\username\.kube\config` 来存储 Kubernetes 集群详细信息，例如端点和凭证。如果你部署了一个集群，你将能够在该位置看到这个文件。但如果你一直使用 master 节点通过 SSH 或其他方法运行你的所有 kubectl 命令，那么这篇文章将有望帮助你掌握如何从工作站进行连接。

然后我们需要从集群获取 kubeconfig 文件，或者我们也可以在部署后从我们的配置文件中获取这个，通过 SCP 获取此文件的内容，或者只是打开一个到你主节点​​的控制台会话，并将其复制到本地 Windows 机器上。

![](Images/Day52_Kubernetes6.png)

然后我们想复制该配置文件并将其移动到我们工作站的 `$HOME/.kube/config` 位置。

![](Images/Day52_Kubernetes7.png)

现在从你的本地工作站，你将能够运行 `kubectl cluster-info` 和 `kubectl get nodes` 来验证你是否有权访问你的集群。

![](Images/Day52_Kubernetes8.png)

这不仅允许从你的 Windows 机器进行连接和控制，而且还允许我们进行一些端口转发，以便从我们的 Windows 机器访问某些服务。

如果你有兴趣了解如何在工作站上管理多个集群，我有一个更详细的演练[在这里](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-6)。

我添加了这个列表，是我做过的关于不同 Kubernetes 集群部署的演练博客。

- [Kubernetes playground – How to choose your platform](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-1)
- [Kubernetes playground – Setting up your cluster](https://vzilla.co.uk/vzilla-blog/building-the-home-lab-kubernetes-playground-part-2)
- [Getting started with Amazon Elastic Kubernetes Service (Amazon EKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-amazon-elastic-kubernetes-service-amazon-eks)
- [Getting started with Microsoft Azure Kubernetes Service (AKS)](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-azure-kubernetes-service-aks)
- [Getting Started with Microsoft AKS – Azure PowerShell Edition](https://vzilla.co.uk/vzilla-blog/getting-started-with-microsoft-aks-azure-powershell-edition)
- [Getting started with Google Kubernetes Service (GKE)](https://vzilla.co.uk/vzilla-blog/getting-started-with-google-kubernetes-service-gke)
- [Kubernetes, How to – AWS Bottlerocket + Amazon EKS](https://vzilla.co.uk/vzilla-blog/kubernetes-how-to-aws-bottlerocket-amazon-eks)
- [Getting started with CIVO Cloud](https://vzilla.co.uk/vzilla-blog/getting-started-with-civo-cloud)
- [Minikube - Kubernetes Demo Environment For Everyone](https://vzilla.co.uk/vzilla-blog/project_pace-kasten-k10-demo-environment-for-everyone)

### 我们将在 Kubernetes 系列中涵盖的内容

我们已经开始介绍下面提到的一些内容，但明天我们将通过第二次集群部署进行更多的实践操作，然后我们就可以开始在集群中部署应用程序了。

- Kubernetes 架构
- Kubectl 命令
- Kubernetes YAML
- Kubernetes Ingress
- Kubernetes 服务
- Helm 包管理器
- 持久化存储
- 有状态应用

## 资源

如果你有使用过的免费资源，请随时通过 PR 将它们添加到这里，我很乐意将它们包含进来。

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [TechWorld with Nana - Kubernetes Tutorial for Beginners [FULL COURSE in 4 Hours]](https://www.youtube.com/watch?v=X48VuDVv0do)
- [TechWorld with Nana - Kubernetes Crash Course for Absolute Beginners](https://www.youtube.com/watch?v=s_o8dwzRlu4)
- [Kunal Kushwaha - Kubernetes Tutorial for Beginners | What is Kubernetes? Architecture Simplified!](https://www.youtube.com/watch?v=KVBON1lA9N8)

[第53天](day53.md)见。
