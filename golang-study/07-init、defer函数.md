# 07 - init、defer 函数

## 1. init 函数

`init()` 是 Go 的特殊函数，会在 `main()` 执行前自动调用。

特点：

- **不能手动调用**。
- **不能作为参数传递**。
- **不能有参数和返回值**。
- **一个 Go 文件可以有多个 `init()`**。
- 同一个文件中，多个 `init()` 按书写顺序执行。

```go
package main

import "fmt"

func init() {
	fmt.Println("init1")
}

func init() {
	fmt.Println("init2")
}

func init() {
	fmt.Println("init3")
}

func main() {
	fmt.Println("main")
}
```

输出：

```text
init1
init2
init3
main
```

## 2. init 执行顺序

程序启动时，大致顺序是：

```text
初始化被依赖的包
-> 初始化当前包
-> const
-> var
-> init()
-> main()
```

图示：

![[Pasted image 20260708144245.png]]

![[Pasted image 20260708144938.png]]

**重点：**`init()` 适合做包初始化，不适合写主要业务逻辑。

## 3. defer 函数

`defer` 用于注册**延迟调用**。

常见用途：

- 关闭文件。
- 释放锁。
- 关闭数据库连接。
- 做函数退出前的清理工作。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("最后执行")

	fmt.Println("先执行")
}
```

输出：

```text
先执行
最后执行
```

## 4. 多个 defer 的执行顺序

多个 `defer` 按**后进先出**执行，离 `return` 越近的越先执行。

```go
package main

import "fmt"

func run() {
	defer fmt.Println("defer2")
	fmt.Println("run")
	defer fmt.Println("defer1")
}

func main() {
	defer fmt.Println("defer4")
	run()
	defer fmt.Println("defer3")
}
```

输出：

```text
run
defer1
defer2
defer3
defer4
```

**重点：**`defer` 是函数级别的。每个函数有自己的 defer 栈，**defer 遵循先进后出原则（栈）**。

## 5. defer 参数求值时机

`defer` 后面的参数，在注册 `defer` 时就已经确定。

```go
package main

import "fmt"

func main() {
	name := "Go"

	defer fmt.Println(name)

	name = "Java"
}
```

输出：

```text
Go
```

**注意：** 不是等到真正执行 `defer` 时才去取参数值。

## 6. 易错点

- **`init()` 不能手动调用**。
- **`init()` 没有参数和返回值**。
- **`main()` 一定在所有相关 `init()` 后执行**。
- **多个 `defer` 后注册的先执行**。
- **`defer` 的参数在注册时确定**。
- **`defer` 常用于资源清理**。
