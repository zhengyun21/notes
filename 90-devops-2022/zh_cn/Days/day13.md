---
title: '#90DaysOfDevOps - 用我们的新应用发送推文进度 - 第13天'
published: false
description: 90DaysOfDevOps - 用我们的新应用发送推文进度
tags: 'devops, 90daysofdevops, learning'
cover_image: null
canonical_url: null
id: 1048865
---
## 用我们的新应用发送推文进度

在探索这门编程语言的最后一天，我们只是触及了这门语言的表面，但我认为这是一个开始，足以让我们产生兴趣、感到兴奋，并想要更深入地了解它。

在过去的几天里，我们从一个小的应用想法出发，逐步为它添加功能。在本次课程中，我想利用我们提到的那些包，为我们的应用创建功能，使其不仅能在屏幕上显示进度更新，还能发送一条推文，包含挑战详情和你的状态。

## 添加发送推文进度的功能

首先，我们需要设置 Twitter 开发者 API 访问权限，这样才能正常工作。

前往 [Twitter Developer Platform](https://developer.twitter.com)，使用你的 Twitter 账号登录。进入后，你应该会看到类似下面的界面（除了我已经创建的应用之外）。

![](Images/Day13_Go1.png)

在这里，你可能还想申请提升访问权限（elevated access），这可能需要一些时间，但对我来说非常快。

接下来，我们应该选择 Projects & Apps 并创建我们的应用。限制取决于你拥有的账户访问权限，使用 essential 级别你只能有一个应用和一个项目，使用 elevated 级别你可以有3个应用。

![](Images/Day13_Go2.png)

给你的应用起个名字

![](Images/Day13_Go3.png)

然后你会获得这些 API 令牌（tokens），重要的是要将它们保存在安全的地方。（我已经删除了这个应用）我们稍后会在 Go 应用中用到这些。

![](Images/Day13_Go4.png)

现在我们的应用已经创建好了，（我不得不更改应用名称，因为截图中的名称已被占用，这些名称需要是唯一的）

![](Images/Day13_Go5.png)

我们之前获取的密钥被称为 consumer keys（消费者密钥），我们还需要 access token（访问令牌）和 secrets（密钥）。我们可以使用 "Keys & Tokens" 标签页来获取这些信息。

![](Images/Day13_Go6.png)

好了，我们在 Twitter 开发者门户的操作暂时告一段落。确保你安全保管好密钥，因为稍后我们会用到它们。

## Go Twitter 机器人

记住我们应用的起始代码在这里 [day13_example1](Go/day13_example1.go)，但首先，我们需要确认我们有正确的代码来发送推文。

我们现在需要考虑如何让我们的输出或消息以推文的形式发送到 Twitter。我们将使用 [go-twitter](https://github.com/dghubble/go-twitter)，这是一个用于 Twitter API 的 Go 客户端库。

在将其放入主应用之前进行测试，我在我们的 `src` 文件夹中创建了一个名为 go-twitter-bot 的新目录，在该文件夹中执行了 `go mod init github.com/michaelcade/go-twitter-bot`，这会创建一个 `go.mod` 文件，然后我们就可以开始编写新的 main.go 并进行测试。

现在我们需要那些从 Twitter 开发者门户获取的密钥、令牌和密钥（secrets）。我们将把它们设置为环境变量。这取决于你运行的操作系统：

Windows
```
set CONSUMER_KEY
set CONSUMER_SECRET
set ACCESS_TOKEN
set ACCESS_TOKEN_SECRET
```

Linux / macOS
```
export CONSUMER_KEY
export CONSUMER_SECRET
export ACCESS_TOKEN
export ACCESS_TOKEN_SECRET
```

在这个阶段，你可以查看 [day13_example2](Go/day13_example2.go) 的代码，但你会看到这里我们使用了一个结构体（struct）来定义我们的密钥、secrets 和令牌。

然后我们有一个 `func` 来解析这些凭据并建立与 Twitter API 的连接。

基于成功连接，我们就会发送一条推文。

```
package main

import (
    // other imports
    "fmt"
    "log"
    "os"

    "github.com/dghubble/go-twitter/twitter"
    "github.com/dghubble/oauth1"
)

// Credentials 存储了我们所有用于认证
// Twitter REST API 所需的访问/消费者令牌
// 和密钥。
type Credentials struct {
    ConsumerKey       string
    ConsumerSecret    string
    AccessToken       string
    AccessTokenSecret string
}

// getClient 是一个辅助函数，它将返回一个 twitter 客户端，
// 随后我们可以用它来发送推文，或流式接收新推文。
// 它接收一个指向 Credential 结构体的指针，其中包含
// 认证所需的一切信息，并返回一个指向 twitter Client 的指针
// 或一个错误。
func getClient(creds *Credentials) (*twitter.Client, error) {
    // 传入你的 consumer key（API Key）和 Consumer Secret（API Secret）
    config := oauth1.NewConfig(creds.ConsumerKey, creds.ConsumerSecret)
    // 传入你的 Access Token 和 Access Token Secret
    token := oauth1.NewToken(creds.AccessToken, creds.AccessTokenSecret)

    httpClient := config.Client(oauth1.NoContext, token)
    client := twitter.NewClient(httpClient)

    // 验证凭据
    verifyParams := &twitter.AccountVerifyParams{
        SkipStatus:   twitter.Bool(true),
        IncludeEmail: twitter.Bool(true),
    }

    // 我们可以获取用户并验证我们使用的凭据
    // 是否成功允许我们登录！
    user, _, err := client.Accounts.VerifyCredentials(verifyParams)
    if err != nil {
        return nil, err
    }

    log.Printf("User's ACCOUNT:\n%+v\n", user)
    return client, nil
}
func main() {
    fmt.Println("Go-Twitter Bot v0.01")
    creds := Credentials{
        AccessToken:       os.Getenv("ACCESS_TOKEN"),
        AccessTokenSecret: os.Getenv("ACCESS_TOKEN_SECRET"),
        ConsumerKey:       os.Getenv("CONSUMER_KEY"),
        ConsumerSecret:    os.Getenv("CONSUMER_SECRET"),
    }

    client, err := getClient(&creds)
    if err != nil {
        log.Println("Error getting Twitter Client")
        log.Println(err)
    }

    tweet, resp, err := client.Statuses.Update("A Test Tweet from the future, testing a #90DaysOfDevOps Program that tweets, tweet tweet", nil)
    if err != nil {
        log.Println(err)
    }
    log.Printf("%+v\n", resp)
    log.Printf("%+v\n", tweet)
}

```

上面的代码会根据发生的情况给你返回一个错误，或者成功执行，你会看到一条推文按照代码中概述的消息发送出去了。

## 将两者结合起来 - Go-Twitter-Bot + 我们的应用

现在我们需要在 `main.go` 中合并这两者。我相信有人会尖叫着说有一种更好的方法来做这件事，请在评论中指出，因为你可以在一个项目中拥有多个 `.go` 文件，这样可能更有意义，但这个方法也能用。

你可以在这里看到合并后的代码库 [day13_example3](Go/day13_example3.go)，但我也会在下面展示。

```
package main

import (
    // other imports
    "fmt"
    "log"
    "os"

    "github.com/dghubble/go-twitter/twitter"
    "github.com/dghubble/oauth1"
)

// Credentials 存储了我们所有用于认证
// Twitter REST API 所需的访问/消费者令牌
// 和密钥。
type Credentials struct {
    ConsumerKey       string
    ConsumerSecret    string
    AccessToken       string
    AccessTokenSecret string
}

// getClient 是一个辅助函数，它将返回一个 twitter 客户端，
// 随后我们可以用它来发送推文，或流式接收新推文。
// 它接收一个指向 Credential 结构体的指针，其中包含
// 认证所需的一切信息，并返回一个指向 twitter Client 的指针
// 或一个错误。
func getClient(creds *Credentials) (*twitter.Client, error) {
    // 传入你的 consumer key（API Key）和 Consumer Secret（API Secret）
    config := oauth1.NewConfig(creds.ConsumerKey, creds.ConsumerSecret)
    // 传入你的 Access Token 和 Access Token Secret
    token := oauth1.NewToken(creds.AccessToken, creds.AccessTokenSecret)

    httpClient := config.Client(oauth1.NoContext, token)
    client := twitter.NewClient(httpClient)

    // 验证凭据
    verifyParams := &twitter.AccountVerifyParams{
        SkipStatus:   twitter.Bool(true),
        IncludeEmail: twitter.Bool(true),
    }

    // 我们可以获取用户并验证我们使用的凭据
    // 是否成功允许我们登录！
    user, _, err := client.Accounts.VerifyCredentials(verifyParams)
    if err != nil {
        return nil, err
    }

    log.Printf("User's ACCOUNT:\n%+v\n", user)
    return client, nil
}
func main() {
    creds := Credentials{
        AccessToken:       os.Getenv("ACCESS_TOKEN"),
        AccessTokenSecret: os.Getenv("ACCESS_TOKEN_SECRET"),
        ConsumerKey:       os.Getenv("CONSUMER_KEY"),
        ConsumerSecret:    os.Getenv("CONSUMER_SECRET"),
    }
    {
        const DaysTotal int = 90
        var remainingDays uint = 90
        challenge := "#90DaysOfDevOps"

        fmt.Printf("Welcome to the %v challenge.\nThis challenge consists of %v days\n", challenge, DaysTotal)

        var TwitterName string
        var DaysCompleted uint

        // 请求用户输入
        fmt.Println("Enter Your Twitter Handle: ")
        fmt.Scanln(&TwitterName)

        fmt.Println("How many days have you completed?: ")
        fmt.Scanln(&DaysCompleted)

        // 计算剩余天数
        remainingDays = remainingDays - DaysCompleted

        //fmt.Printf("Thank you %v for taking part and completing %v days.\n", TwitterName, DaysCompleted)
        //fmt.Printf("You have %v days remaining for the %v challenge\n", remainingDays, challenge)
        // fmt.Println("Good luck")

        client, err := getClient(&creds)
        if err != nil {
            log.Println("Error getting Twitter Client, this is expected if you did not supply your Twitter API tokens")
            log.Println(err)
        }

        message := fmt.Sprintf("Hey I am %v I have been doing the %v for %v days and I have %v Days left", TwitterName, challenge, DaysCompleted, remainingDays)
        tweet, resp, err := client.Statuses.Update(message, nil)
        if err != nil {
            log.Println(err)
        }
        log.Printf("%+v\n", resp)
        log.Printf("%+v\n", tweet)
    }

}
```

这个结果应该是一条推文，但如果你没有提供环境变量，那么你应该会收到类似下面的错误。

![](Images/Day13_Go7.png)

一旦你修复了这个问题，或者如果你选择不进行 Twitter 认证，那么你可以使用昨天完成的代码。成功时的终端输出看起来类似这样：

![](Images/Day13_Go8.png)

生成的推文应该看起来像这样：

![](Images/Day13_Go9.png)

## 如何为多个操作系统编译

接下来我想回答一个问题："如何为多个操作系统编译？" Go 的一大优点是它可以轻松地为许多不同的操作系统编译。你可以通过运行以下命令获取完整列表：

```
go tool dist list
```

到目前为止，我们使用的 `go build` 命令很好，它会使用 `GOOS` 和 `GOARCH` 环境变量来确定主机机器以及构建的目标。但我们也可以使用下面的代码来创建其他二进制文件。

```
GOARCH=amd64 GOOS=darwin go build -o ${BINARY_NAME}_0.1_darwin main.go
GOARCH=amd64 GOOS=linux go build -o ${BINARY_NAME}_0.1_linux main.go
GOARCH=amd64 GOOS=windows go build -o ${BINARY_NAME}_0.1_windows main.go
GOARCH=arm64 GOOS=linux go build -o ${BINARY_NAME}_0.1_linux_arm64 main.go
GOARCH=arm64 GOOS=darwin go build -o ${BINARY_NAME}_0.1_darwin_arm64 main.go
```

这将为上述所有平台在你的目录中生成二进制文件。然后你可以将其创建一个 makefile，以便在添加新功能和功能时构建这些二进制文件。我已包含 [makefile](Go/makefile)

这就是我用来创建你现在可以在 [仓库](https://github.com/MichaelCade/90DaysOfDevOps/releases) 上看到的发布版本的方法。

## 资源

- [StackOverflow 2021 Developer Survey](https://insights.stackoverflow.com/survey/2021)
- [Why we are choosing Golang to learn](https://www.youtube.com/watch?v=7pLqIIAqZD4&t=9s)
- [Jake Wright - Learn Go in 12 minutes](https://www.youtube.com/watch?v=C8LgvuEBraI&t=312s)
- [Techworld with Nana - Golang full course - 3 hours 24 mins](https://www.youtube.com/watch?v=yyUHQIec83I)
- [**NOT FREE** Nigel Poulton Pluralsight - Go Fundamentals - 3 hours 26 mins](https://www.pluralsight.com/courses/go-fundamentals)
- [FreeCodeCamp -  Learn Go Programming - Golang Tutorial for Beginners](https://www.youtube.com/watch?v=YS4e4q9oBaU&t=1025s)
- [Hitesh Choudhary - Complete playlist](https://www.youtube.com/playlist?list=PLRAV69dS1uWSR89FRQGZ6q9BR2b44Tr9N)
- [A great repo full of all things DevOps & exercises](https://github.com/bregman-arie/devops-exercises)
- [GoByExample - Example based learning](https://gobyexample.com/)
- [go.dev/tour/list](https://go.dev/tour/list)
- [go.dev/learn](https://go.dev/learn/)

这结束了为期7天的编程语言学习！还有更多的内容可以覆盖，我希望你能够继续学习以上内容，并能够理解 Go 编程语言的其他方面。

接下来，我们将把注意力转向 Linux 以及一些我们都应该知道的基础知识。

[第14天](day14.md)见。
