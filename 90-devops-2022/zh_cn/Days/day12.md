---
title: '#90DaysOfDevOps - 使用指针获取用户输入并完成程序 - 第12天'
published: false
description: 90DaysOfDevOps - 使用指针获取用户输入并完成程序
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048864
---
## 使用指针获取用户输入并完成程序

昨天（[第11天](day11.md)），我们创建了第一个独立的Go程序，其中需要用户输入的部分是在代码中以变量的形式创建并赋值的，现在我们要向用户询问输入，以便为最终的输出消息赋值。

## 获取用户输入

在此之前，让我们再次查看我们的应用程序，并在获取用户输入之前先过一遍我们想要测试的变量。

昨天我们最终完成的代码看起来像这样 [day11_example4.go](Go/day11_example4.go)，我们在代码中手动定义了 `challenge, daystotal, dayscomplete` 变量和常量。

现在让我们添加一个名为 `TwitterName` 的新变量，你可以在 [day12_example1.go](Go/day12_example1.go) 找到这个新代码，如果我们运行这段代码，这就是我们的输出。

![](Images/Day12_Go1.png)

我们现在在第12天，如果 `dayscomplete` 是硬编码的，我们就需要每天修改它并重新编译代码，这听起来不太好。

获取用户输入时，我们想要获取一个名字的值和已完成的天数。为此，我们可以使用 `fmt` 包中的另一个函数。

回顾 `fmt` 包，它有不同功能用于：格式化输入和输出（I/O）

- 打印消息
- 收集用户输入
- 写入文件

这里我们不是直接给变量赋值，而是询问用户输入。

```
fmt.Scan(&TwitterName)
```
注意我们在变量前也使用了 `&`。这就是指针（pointer），我们将在下一节介绍。

在我们的代码 [day12_example2.go](Go/day12_example2.go) 中，你可以看到我们要求用户输入两个变量，`TwitterName` 和 `DaysCompleted`。

现在让我们运行程序，你会看到我们可以输入上面两个值。

![](Images/Day12_Go2.png)

好的，我们获取了用户输入并打印了一条消息，但是否能让程序告诉我们挑战中还剩多少天呢？

为此，我们创建了一个名为 `remainingDays` 的变量，并在代码中将其硬编码为 `90`，然后我们需要改变这个值，以便在获取用户输入的 `DaysCompleted` 后打印剩余天数，我们可以通过简单的变量运算来实现。

```
remainingDays = remainingDays - DaysCompleted
```
你可以在这里看到我们完成的程序的样子 [day12_example2.go](Go/day12_example3.go)。

如果我们现在运行这个程序，你可以看到基于用户输入和 `remainingDays` 的值进行了简单的计算。

![](Images/Day12_Go3.png)

## 什么是指针？（特殊变量）

指针是一种（特殊的）变量，它指向另一个变量的内存地址。

关于这一点的一个很好的解释可以在 [geeksforgeeks](https://www.geeksforgeeks.org/pointers-in-golang/) 找到。

现在让我们简化我们的代码，展示在一个打印命令前使用和不使用 `&` 的区别，这会给我们指针的内存地址。我已将代码示例添加在这里。[day12_example4.go](Go/day12_example4.go)

下面是运行这段代码的结果。

![](Images/Day12_Go4.png)

## 资源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp -  Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)

[第13天](day13.md)见。
