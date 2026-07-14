---
title: '#90DaysOfDevOps - 测试、工具与替代方案 - 第62天'
published: false
description: '90DaysOfDevOps - 测试、工具与替代方案'
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1049053
---
## 测试、工具与替代方案

在我们结束基础设施即代码（Infrastructure as Code）这一节时，我们必须提到测试我们的代码、可用的各种不同工具，以及Terraform的一些替代方案来实现这一目标。正如我在本节开头所说，我关注Terraform的原因是它首先是免费和开源的，其次是它跨平台且与环境无关。但市面上也有其他替代方案值得考虑，而总体目标是让人们意识到这是部署基础设施的方式。

### 代码腐化（Code Rot）

本节我要讲的第一个领域是代码腐化，与应用程序代码不同，基础设施即代码可能使用一次后很长时间不再使用。举个例子，我们将使用Terraform在AWS中部署我们的VM环境，第一次就完美运行，我们得到了环境，但这个环境不会经常改变，所以代码就被搁置了，状态可能（或希望）存储在中央位置，但代码本身不会改变。

如果基础设施中发生了变化怎么办？但它是通过带外（out of band）方式完成的，或者环境中发生了其他变化。

- 带外变更
- 未固定版本
- 已弃用的依赖
- 未应用的变更

### 测试

另一个紧随代码腐化之后的巨大领域是能够测试你的IaC，并确保所有区域都按预期工作。

首先，有一些内置的测试命令我们可以看看：

| 命令                  | 描述                                                                                       |
| --------------------- | ------------------------------------------------------------------------------------------ |
| `terraform fmt`       | 将Terraform配置文件重写为规范的格式和样式。                                                |
| `terraform validate`  | 验证目录中的配置文件，仅引用配置本身。                                                     |
| `terraform plan`      | 创建一个执行计划，让你可以预览Terraform计划进行的更改。                                    |
| Custom validation     | 验证你的输入变量，确保它们符合你的预期。                                                   |

我们还有一些Terraform之外可用的测试工具：

- [tflint](https://github.com/terraform-linters/tflint)

    - 发现可能的错误
    - 警告已弃用的语法、未使用的声明。
    - 强制执行最佳实践、命名规范。

扫描工具

- [checkov](https://www.checkov.io/) - 扫描云基础设施配置，在部署之前发现错误配置。
- [tfsec](https://aquasecurity.github.io/tfsec/v1.4.2/) - 针对你的Terraform代码的静态分析安全扫描器。
- [terrascan](https://github.com/accurics/terrascan) - 针对基础设施即代码的静态代码分析器。
- [terraform-compliance](https://terraform-compliance.com/) - 一个轻量级、专注于安全和合规性的测试框架，针对terraform，为你的基础设施即代码启用负面测试能力。
- [snyk](https://docs.snyk.io/products/snyk-infrastructure-as-code/scan-terraform-files/scan-and-fix-security-issues-in-terraform-files) - 扫描你的Terraform代码中的错误配置和安全问题。

托管云服务

- [Terraform Sentinel](https://www.terraform.io/cloud-docs/sentinel) - 嵌入在HashiCorp企业产品中的策略即代码（policy-as-code）框架。它支持细粒度的、基于逻辑的策略决策，并可以扩展以使用来自外部源的信息。

自动化测试

- [Terratest](https://terratest.gruntwork.io/) - Terratest是一个Go库，提供了用于测试基础设施的模式和辅助函数。

值得一提

- [Terraform Cloud](https://cloud.hashicorp.com/products/terraform) - Terraform Cloud是HashiCorp的托管服务产品。它消除了从业者、团队和组织在生产环境中使用Terraform时不必要的工具和文档需求。

- [Terragrunt](https://terragrunt.gruntwork.io/) - Terragrunt是一个轻量级包装器，提供了额外的工具来保持你的配置DRY（Don't Repeat Yourself），处理多个Terraform模块，以及管理远程状态。

- [Atlantis](https://www.runatlantis.io/) - Terraform Pull Request自动化工具。

### 替代方案

我们在第57天开始本节时提到过有一些替代方案，我非常计划在完成这个挑战后进一步探索它们。

| 云特定                          | 云无关         |
| ------------------------------- | -------------- |
| AWS CloudFormation              | Terraform      |
| Azure Resource Manager          | Pulumi         |
| Google Cloud Deployment Manager |                |

在上述列表中，我使用AWS CloudFormation可能是最多的，它是AWS原生的，但我没有使用过除Terraform之外的其他工具。正如你可以想象的那样，云特定的版本在特定云环境中非常好，但如果你有多个云环境，那么你将很难迁移这些配置，或者你将需要多个管理平面来管理你的IaC工作。

我认为对我来说，一个有趣的下一步是花些时间更多地了解[Pulumi](https://www.pulumi.com/)。

来自Pulumi官网的比较

*"Terraform和Pulumi都提供了期望状态的基础设施即代码模型，其中代码代表期望的基础设施状态，部署引擎将这个期望状态与堆栈的当前状态进行比较，并确定需要创建、更新或删除哪些资源。"*

我能看到的最大区别是，与HashiCorp配置语言（HCL）不同，Pulumi允许使用通用编程语言，如Python、TypeScript、JavaScript、Go和.NET。

一个快速概览[Introduction to Pulumi: Modern Infrastructure as Code](https://www.youtube.com/watch?v=QfJTJs24-JM)，我喜欢它所展示的易用性和选择，想进一步了解它。

这结束了基础设施即代码部分，接下来我们将进入与配置管理有一点重叠的领域，特别是当我们越过配置管理的全景图后，我们将使用Ansible来完成其中一些任务和演示。

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
- [Pulumi - IaC in your favorite programming language!](https://www.youtube.com/watch?v=vIjeiDcsR3Q&t=51s)

[第63天](day63.md)见。
