# 10 - 协程和 channel

## 1. goroutine 是什么

`goroutine` 是 Go 运行时管理的**轻量级线程**。

在函数前加 `go`，这个函数就会并发执行。

```go
package main

import "fmt"

func say() {
	fmt.Println("hello")
}

func main() {
	go say()
	fmt.Println("main")
}
```

**注意：**`main` 结束后，其他 goroutine 也会跟着结束。

## 2. 等待 goroutine 结束

不要靠 `time.Sleep` 硬等。更常用的是 `sync.WaitGroup`。

```go
package main

import (
	"fmt"
	"sync"
)

func work(id int, wg *sync.WaitGroup) {
	defer wg.Done()
	fmt.Println("任务", id, "完成")
}

func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 3; i++ {
		wg.Add(1)
		go work(i, &wg)
	}

	wg.Wait()
	fmt.Println("全部完成")
}
```

记法：

- `Add(1)`：增加一个任务。
- `Done()`：完成一个任务。
- `Wait()`：等待所有任务完成。

## 3. channel 是什么

`channel` 用来在 goroutine 之间传递数据。

```go
package main

import "fmt"

func main() {
	ch := make(chan string)

	go func() {
		ch <- "hello"
	}()

	msg := <-ch
	fmt.Println(msg)
}
```

**重点：**

- `ch <- value`：发送数据。
- `value := <-ch`：接收数据。

## 4. 无缓冲 channel

默认创建的是无缓冲 channel。

```go
ch := make(chan int)
```

无缓冲 channel 的特点：

- 发送方会等接收方。
- 接收方会等发送方。
- 两边都准备好，数据才会传过去。

**一句话：**无缓冲 channel 像“当面交东西”。

## 5. 有缓冲 channel

有缓冲 channel 可以先存几个值。

```go
package main

import "fmt"

func main() {
	ch := make(chan int, 2)

	ch <- 1
	ch <- 2

	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

**注意：** 缓冲满了再发送会阻塞，空了再接收也会阻塞。

## 6. 关闭 channel

发送方可以用 `close` 表示：以后不会再发送数据了。

```go
package main

import "fmt"

func main() {
	ch := make(chan int)

	go func() {
		ch <- 1
		ch <- 2
		ch <- 3
		close(ch)
	}()

	for n := range ch {
		fmt.Println(n)
	}
}
```

**重点：**`range ch` 会一直接收数据，直到 channel 被关闭。

## 7. 判断 channel 是否关闭

接收 channel 时，可以用第二个返回值判断是否还能继续接收。

```go
package main

import "fmt"

func main() {
	ch := make(chan int, 1)

	ch <- 10
	close(ch)

	n, ok := <-ch
	fmt.Println(n, ok)

	n, ok = <-ch
	fmt.Println(n, ok)
}
```

输出类似：

```text
10 true
0 false
```

**注意：** 关闭后的 channel 不能再发送数据。

## 8. select

`select` 用来同时等待多个 channel。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	ch1 := make(chan string)
	ch2 := make(chan string)

	go func() {
		time.Sleep(1 * time.Second)
		ch1 <- "A"
	}()

	go func() {
		time.Sleep(2 * time.Second)
		ch2 <- "B"
	}()

	select {
	case msg := <-ch1:
		fmt.Println(msg)
	case msg := <-ch2:
		fmt.Println(msg)
	}
}
```

**重点：**哪个 channel 先准备好，`select` 就执行哪个分支。

## 9. 超时处理

`select` 常和 `time.After` 一起做超时控制。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	done := make(chan struct{})

	go func() {
		time.Sleep(2 * time.Second)
		close(done)
	}()

	select {
	case <-done:
		fmt.Println("完成")
	case <-time.After(1 * time.Second):
		fmt.Println("超时")
	}
}
```

**重点：**`struct{}` 常用来做“只通知，不传值”的信号。

## 10. 易错点

- **`main` 结束后，其他 goroutine 也会结束**。
- **等待 goroutine 优先用 `WaitGroup`**。
- **无缓冲 channel 发送和接收会互相等待**。
- **有缓冲 channel 满了会阻塞，空了也会阻塞**。
- **通常由发送方关闭 channel**。
- **关闭后的 channel 不能再发送数据**。
- **`range channel` 会在 channel 关闭后结束**。
- **`select` 用来等待多个 channel**。
