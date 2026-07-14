---
title: '#90DaysOfDevOps - 使用Terraform和变量创建虚拟机 - 第59天'
published: false
description: 90DaysOfDevOps - 使用Terraform和变量创建虚拟机
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1049051
---
## 使用Terraform和变量创建虚拟机

在本节中，我们将使用Terraform在VirtualBox中创建一个或两个虚拟机。这并非常规做法，VirtualBox是一种工作站虚拟化方案，实际上并不是Terraform的典型使用场景，但我目前在36,000英尺的高空，虽然我也曾在云端部署过公有云资源，但在本地笔记本上操作会快得多。

纯粹是演示目的，但概念是一样的：我们将编写期望状态的配置代码，然后针对VirtualBox提供程序（provider）运行它。过去我们在这里使用过Vagrant，我在本节开头也介绍了Vagrant和Terraform之间的区别。

### 在VirtualBox中创建虚拟机

首先，我们要创建一个名为virtualbox的新文件夹，然后创建一个virtualbox.tf文件，这就是我们定义资源的地方。下面的代码可以在VirtualBox文件夹中的virtualbox.tf文件里找到，它将在VirtualBox中创建2个虚拟机。

你可以在[这里](https://registry.terraform.io/providers/terra-farm/virtualbox/latest/docs/resources/vm)找到更多关于社区VirtualBox提供程序的信息。

```
terraform {
  required_providers {
    virtualbox = {
      source = "terra-farm/virtualbox"
      version = "0.2.2-alpha.1"
    }
  }
}

# There are currently no configuration options for the provider itself.

resource "virtualbox_vm" "node" {
  count     = 2
  name      = format("node-%02d", count.index + 1)
  image     = "https://app.vagrantup.com/ubuntu/boxes/bionic64/versions/20180903.0.0/providers/virtualbox.box"
  cpus      = 2
  memory    = "512 mib"

  network_adapter {
    type           = "hostonly"
    host_interface = "vboxnet1"
  }
}

output "IPAddr" {
  value = element(virtualbox_vm.node.*.network_adapter.0.ipv4_address, 1)
}

output "IPAddr_2" {
  value = element(virtualbox_vm.node.*.network_adapter.0.ipv4_address, 2)
}

```

现在我们已经定义好了代码，可以在文件夹上执行`terraform init`，以下载VirtualBox的提供程序。

![](Images/Day59_IAC1.png)

显然，你的系统上还需要安装VirtualBox。接下来我们可以运行`terraform plan`，看看我们的代码将为我们创建什么。然后运行`terraform apply`，下图展示了完整的流程。

![](Images/Day59_IAC2.png)

在VirtualBox中，你现在会看到2台虚拟机。

![](Images/Day59_IAC3.png)

### 更改配置

让我们在我们的部署中再添加一个节点。我们可以简单地更改count行，以显示我们新期望的节点数量。当我们运行`terraform apply`时，它看起来会像下面这样。

![](Images/Day59_IAC4.png)

一旦完成，在VirtualBox中你可以看到我们现在有3个节点在运行。

![](Images/Day59_IAC5.png)

当我们完成后，可以使用`terraform destroy`来清理，我们的虚拟机将被移除。

![](Images/Day59_IAC6.png)

### 变量与输出

在上节课运行hello-world示例时，我们确实提到了输出（outputs）。但在这里我们可以更详细地了解。

此外，这里还有许多其他变量可以使用，也有几种不同的方式来定义变量。

- 我们可以在运行`terraform plan`或`terraform apply`命令时手动输入变量

- 我们可以在.tf文件的块中定义它们

- 我们可以使用系统中的环境变量，格式为`TF_VAR_NAME`。

- 我更喜欢在项目文件夹中使用terraform.tfvars文件。

- 还有*auto.tfvars文件选项

- 或者我们可以在运行`terraform plan`或`terraform apply`时使用`-var`或`-var-file`来定义。

从下到上将是变量定义的优先级顺序。

我们还提到状态文件（state file）会包含敏感信息。我们可以将敏感信息定义为变量，并将其标记为敏感。

```
variable "some resource"  {
    description = "something important"
    type: string
    sensitive = true

}
```

## 相关资料
我在下面列出了很多资料，我认为这个话题已经被反复讨论过很多次了。如果你有额外的资源，请务必提交PR，我很乐意审查并将它们添加到列表中。

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

[第60天](day60.md)见。
