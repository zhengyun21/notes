# 11 - 并发安全和 sync.Map

## 1. 什么是并发安全

多个 goroutine 同时读写同一份数据时，如果没有保护，就可能出现**数据竞争**。

```go
package main

import (
	"fmt"
	"sync"
)

var num int

func main() {
	var wg sync.WaitGroup

	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 0; i < 10000; i++ {
			num++
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10000; i++ {
			num--
		}
	}()

	wg.Wait()
	fmt.Println(num)
}
```

理论上结果应该是 `0`，但实际结果可能不稳定。

**原因：**`num++` 和 `num--` 不是一步完成的，中途可能被其他 goroutine 打断。

## 2. 使用 Mutex 加锁

`sync.Mutex` 是互斥锁。一个 goroutine 加锁后，其他 goroutine 必须等待。

```go
package main

import (
	"fmt"
	"sync"
)

var num int
var mu sync.Mutex

func main() {
	var wg sync.WaitGroup

	wg.Add(2)

	go func() {
		defer wg.Done()
		for i := 0; i < 10000; i++ {
			mu.Lock()
			num++
			mu.Unlock()
		}
	}()

	go func() {
		defer wg.Done()
		for i := 0; i < 10000; i++ {
			mu.Lock()
			num--
			mu.Unlock()
		}
	}()

	wg.Wait()
	fmt.Println(num)
}
```

**重点：**同一份共享数据，读写时要用同一把锁保护。

## 3. 用 defer 释放锁

如果函数里逻辑多，推荐用 `defer` 保证解锁。

```go
func add() {
	mu.Lock()
	defer mu.Unlock()

	num++
}
```

**注意：**锁住的代码越少越好。不要把无关逻辑也放进锁里。

## 4. map 不是并发安全的

普通 `map` 不能被多个 goroutine 同时读写。

```go
var m = map[string]int{}

go func() {
	m["age"] = 18
}()

go func() {
	fmt.Println(m["age"])
}()
```

可能报错：

```text
fatal error: concurrent map read and map write
```

**重点：**普通 `map` 并发读写时，要加锁。

## 5. map 加锁

用 `Mutex` 保护普通 `map`。

```go
package main

import (
	"fmt"
	"sync"
)

var m = map[string]int{}
var mu sync.Mutex

func main() {
	mu.Lock()
	m["age"] = 18
	mu.Unlock()

	mu.Lock()
	age := m["age"]
	mu.Unlock()

	fmt.Println(age)
}
```

**注意：**读和写都要加锁，只给写加锁不够安全。

## 6. sync.Map

`sync.Map` 是 Go 提供的并发安全 map。

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var m sync.Map

	m.Store("name", "Go")

	value, ok := m.Load("name")
	fmt.Println(value, ok)

	m.Delete("name")
}
```

常用方法：

- `Store(key, value)`：保存值。
- `Load(key)`：读取值。
- `Delete(key)`：删除值。
- `Range(func(key, value any) bool)`：遍历。

## 7. 遍历 sync.Map

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var m sync.Map

	m.Store("name", "Go")
	m.Store("age", 18)

	m.Range(func(key, value any) bool {
		fmt.Println(key, value)
		return true
	})
}
```

`Range` 中返回 `true` 表示继续遍历，返回 `false` 表示停止。

## 8. Mutex map 和 sync.Map 怎么选

简单记：

- 普通业务 map：优先用 **`map + Mutex`**。
- 读多写少、key 比较稳定、并发访问频繁：可以考虑 **`sync.Map`**。

**重点：**`sync.Map` 内部也做了并发控制，不是“没有锁”。

## 9. 易错点

- **多个 goroutine 同时读写共享变量，会有数据竞争**。
- **`num++` 不是原子操作**。
- **加锁后一定要解锁**。
- **普通 map 不是并发安全的**。
- **map 的读和写都需要保护**。
- **`sync.Map` 的 `Load` 返回 `value, ok`**。
- **大多数普通场景，用 `map + Mutex` 更直观**。
