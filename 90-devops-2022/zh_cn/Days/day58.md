---
title: '#90DaysOfDevOps - HashiCorp 配置语言 (HCL) - 第58天'
published: false
description: 90DaysOfDevOps - HashiCorp 配置语言 (HCL)
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048741
---
## HashiCorp 配置语言 (HCL)

在我们开始使用 Terraform 创建东西之前，我们必须稍微深入了解一下 HashiCorp Configuration Language (HCL)。到目前为止，在我们的挑战中，我们已经了解了几种不同的脚本和编程语言，这里又是一种。我们接触过 [Go 编程语言](Days/day07.md)，然后是 [bash 脚本](Days/day19.md)，我们甚至在学习 [网络自动化](Days/day27.md) 时接触过一点 Python。

现在我们必须介绍 HashiCorp Configuration Language (HCL)。如果这是你第一次看到这个语言，它可能看起来有点令人生畏，但它非常简单且非常强大。

在本节中，我们将使用可以在本地系统上运行的示例，无论你使用什么操作系统，我们将使用 VirtualBox，尽管这不是你通常与 Terraform 一起使用的基础设施平台。然而，在本地运行它是免费的，并且将使我们能够实现这篇文章中想要达到的目标。我们也可以将这篇文章的概念扩展到 Docker 或 Kubernetes。

但一般来说，你应该使用 Terraform 在公共云（AWS、Google、Microsoft Azure）中部署你的基础设施，也应该在你的虚拟化环境中部署，例如（VMware、Microsoft Hyper-V、Nutanix AHV）。在公共云中，Terraform 允许我们做很多比仅仅自动部署虚拟机更多的事情，我们可以创建所有所需的基础设施，如 PaaS 工作负载以及所有所需的网络资产，如 VPC 和安全组。

Terraform 有两个重要方面，我们有代码（我们将在本文中介绍），然后我们还有状态。这两者合起来可以称为 Terraform 核心。然后我们还有我们希望与之交互并部署到的环境，这是使用 Terraform Provider 执行的，在上一节中简要提到过，但我们有 AWS Provider、Azure Provider 等。有数百个。

### Terraform 基本用法

让我们看一下 Terraform `.tf` 文件，看看它们是如何构成的。我们将要介绍的第一个示例实际上是将资源部署到 AWS 的代码，这还需要在你的系统上安装 AWS CLI 并为你的账户进行配置。


### Provider

在我们的 `.tf` 文件结构的顶部，通常称为 `main.tf`，至少在我们使事情变得更复杂之前。在这里，我们将定义我们之前提到的 Provider。正如你所看到的，我们的 AWS Provider 的来源是 `hashicorp/aws`，这意味着该 Provider 由 HashiCorp 自己维护或发布。默认情况下，你将引用来自 [Terraform Registry](https://registry.terraform.io/) 的可用 Provider，你也可以选择编写自己的 Provider，并在本地使用，或自行发布到 Terraform Registry。

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}
```
我们也可以在这里添加一个区域，以确定我们希望配置到哪个 AWS 区域，我们可以通过添加以下内容来做到这一点：

```
provider "aws" {
  region = "ap-southeast-1" //需要部署资源的区域
}
```

### 资源（Resources）

- Terraform 配置文件的另一个重要组成部分，它描述了一个或多个基础设施对象，如 EC2、负载均衡器、VPC 等。

- 一个 resource 块声明了一个给定类型（"aws_instance"）和给定本地名称（"90daysofdevops"）的资源。

- 资源类型和名称共同作为给定资源的标识符。


```
resource "aws_instance" "90daysofdevops" {
  ami               = data.aws_ami.instance_id.id
  instance_type     = "t2.micro"
  availability_zone = "us-west-2a"
  security_groups   = [aws_security_group.allow_web.name]
  user_data         = <<-EOF
                #! /bin/bash
                sudo yum update
                sudo yum install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd
                echo "
<h1>Deployed via Terraform</h1>

" | sudo tee /var/www/html/index.html
        EOF
  tags = {
    Name = "Created by Terraform"
  }
}
```

从上面的代码中，你还可以看到我们正在运行 `yum` 更新并在我们的 EC2 实例中安装 `httpd`。

如果我们现在看完整的 main.tf 文件，它可能看起来像这样。

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}

provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_instance" "90daysofdevops" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"
  availability_zone = "us-west-2a"
    user_data         = <<-EOF
                #! /bin/bash
                sudo yum update
                sudo yum install -y httpd
                sudo systemctl start httpd
                sudo systemctl enable httpd
                echo "
<h1>Deployed via Terraform</h1>

" | sudo tee /var/www/html/index.html
        EOF
  tags = {
    Name = "Created by Terraform"


  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```
上面的代码将部署一个非常简单的 Web 服务器作为 AWS 中的 EC2 实例，这样做的好处是，我们可以重复执行此操作，并且每次都会得到相同的输出。除了我可能搞砸了代码之外，上述操作没有人机交互。

我们可以看一个超级简单的示例，一个你可能永远不会使用的示例，但让我们姑且幽默一下。像所有好的脚本和编程语言一样，我们应该从一个 hello-world 场景开始。

```
terraform {
  # This module is now only being tested with Terraform 0.13.x. However, to make upgrading easier, we are setting
  # 0.12.26 as the minimum version, as that version added support for required_providers with source URLs, making it
  # forwards compatible with 0.13.x code.
  required_version = ">= 0.12.26"
}

# website::tag::1:: 最简单的 Terraform 模块：它只输出 "Hello, World!"
output "hello_world" {
  value = "Hello, 90DaysOfDevOps from Terraform"
}
```
你会在 IAC 文件夹下的 hello-world 中找到这个文件，但开箱即用地，这不会简单地工作，我们需要运行一些命令才能使用我们的 Terraform 代码。

在终端中导航到创建 main.tf 的文件夹，这可以来自本仓库，或者你可以使用上面的代码创建一个新的。

在该文件夹中，我们将运行 `terraform init`

我们需要在任何拥有或运行任何 Terraform 代码的目录中执行此操作。初始化配置目录会下载并安装配置中定义的 Provider，在这个例子中我们没有 Provider，但在上面的示例中，这将下载该配置的 AWS Provider。

![](Images/Day58_IAC1.png)

下一个命令将是 `terraform plan`

`terraform plan` 命令创建一个执行计划，让你预览 Terraform 计划对基础设施进行的更改。

你可以在下图中简单地看到，对于我们的 hello-world 示例，我们将看到一个输出；如果这是一个 AWS EC2 实例，我们会看到我们将要创建的所有步骤。

![](Images/Day58_IAC2.png)

此时，我们已经初始化了我们的仓库，并且已经在需要的地方下载了我们的 Provider，我们运行了一次测试演练以确保这是我们想要看到的，所以现在我们可以运行并部署我们的代码。

`terraform apply` 允许我们这样做。此命令内置了一个安全措施，它会再次给你一个计划视图，显示将要发生什么，这需要你回复 yes 才能继续。

![](Images/Day58_IAC3.png)

当我们输入 yes 并按回车键后，我们的代码就部署了。显然，这并不那么令人兴奋，但你可以看到我们在代码中定义的输出。

![](Images/Day58_IAC4.png)

现在我们没有部署任何东西，我们没有添加、更改或销毁任何东西，但如果我们这样做了，我们也会在上面看到相应的指示。然而，如果我们已经部署了某些东西，并且我们想要清除我们部署的所有内容，我们可以使用 `terraform destroy` 命令。同样，这也有一个安全措施，你必须输入 yes，尽管你可以在 `apply` 和 `destroy` 命令的末尾使用 `--auto-approve` 来绕过手动干预。但我建议只在学习和测试时使用这个快捷方式，因为所有东西都会消失，有时比建造它们的速度还快。

从这一点来看，我们实际上已经涵盖了 Terraform CLI 的 4 个命令。

- `terraform init` = 让你的项目文件夹准备好 Provider
- `terraform plan` = 显示基于我们的代码，下一个命令将要创建、更改的内容。
- `terraform apply` = 将去部署我们代码中定义的资源。
- `terraform destroy` = 将销毁我们在项目中创建的资源

我们还涵盖了代码文件的两个重要方面。

- Provider = Terraform 如何通过 API 与终端平台通信
- 资源 = 我们想用代码部署什么

另一件要注意的事情是，当运行 `terraform init` 时，查看文件夹运行前后的目录树，看看发生了什么，以及我们在哪里存储 Provider 和模块。

### Terraform 状态

我们还需要了解在我们的目录中创建的状态文件，对于这个 hello world 示例，我们的状态文件很简单。这是一个 JSON 文件，是 Terraform 眼中的世界表示。状态文件会很乐意展示你的敏感数据，所以要小心，并且作为最佳实践，在将 `.tfstate` 文件上传到 GitHub 之前，先将它们放入你的 `.gitignore` 文件夹中。

默认情况下，正如你所看到的，状态文件与你的项目代码位于同一目录中，但它也可以作为选项远程存储。在生产环境中，这很可能是一个共享位置，例如 S3 存储桶。

另一个选项可以是 Terraform Cloud，这是一个付费的托管服务。（最多 5 个用户免费）

将状态存储在远程位置的优点是，我们获得：

- 敏感数据加密
- 协作
- 自动化
- 然而它可能会增加复杂性

```
{
  "version": 4,
  "terraform_version": "1.1.6",
  "serial": 1,
  "lineage": "a74296e7-670d-0cbb-a048-f332696ca850",
  "outputs": {
    "hello_world": {
      "value": "Hello, 90DaysOfDevOps from Terraform",
      "type": "string"
    }
  },
  "resources": []
}
```


## 资源

我在下面列出了很多资源，我认为这个话题已经被涵盖了很多次，如果你有额外的资源，请务必提出 PR，我会很乐意审查并将它们添加到列表中。

- [What is Infrastructure as Code? Difference of Infrastructure as Code Tools](https://www.youtube.com/watch?v=POPP2WTJ8es)
- [Terraform Tutorial | Terraform Course Overview 2021](https://www.youtube.com/watch?v=m3cKkYXl-8o)
- [Terraform explained in 15 mins | Terraform Tutorial for Beginners](https://www.youtube.com/watch?v=l5k1ai_GBDE)
- [Terraform Course - From BEGINNER to PRO!](https://www.youtube.com/watch?v=7xngnjfIlK4&list=WL&index=141&t=16s)
- [HashiCorp Terraform Associate Certification Course](https://www.youtube.com/watch?v=V4waklkBC38&list=WL&index=55&t=111s)
- [Terraform Full Course for Beginners](https://www.youtube.com/watch?v=EJ3N-hhiWv0&list=WL&index=39&t=27s)
- [KodeKloud -  Terraform for DevOps Beginners + Labs: Complete Step by Step Guide!](https://www.youtube.com/watch?v=YcJ9IeukJL8&list=WL&index=16&t=11s)
- [Terraform Simple Projects](https://terraform.joshuajebaraj.com/)
- [Terraform Tutorial - The Best Project Ideas](https://www.youtube.com/watch?v=oA-pPa0vfks)
- [Awesome Terraform](https://github.com/shuaibiyy/awesome-terraform)

[第59天](day59.md)见
