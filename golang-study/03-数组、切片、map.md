# 03 - 数组、切片、map

## 学习目标

- 理解数组和切片的区别。
- 掌握索引、切片表达式、`append`、`make`、`len`、`cap` 的基本用法。
- 学会创建、读取、修改、删除 `map` 中的数据。
- 记住数组、切片、map 常见的易错点。

## 1. 数组

数组用于保存**固定长度**、**相同类型**的一组数据。

数组有两个核心规则：

- 数组里的元素必须是**同一种类型**。
- 声明数组时必须指定**长度**，或者让 Go 根据初始化值自动推断长度。

```go
package main

import "fmt"

func main() {
	var array [3]int = [3]int{1, 2, 3}
	var array1 = [3]int{1, 2, 3}
	var array2 = [...]int{1, 2, 3}

	fmt.Println(array)
	fmt.Println(array1)
	fmt.Println(array2)
}
```

**重点：**数组的长度也是类型的一部分，`[3]int` 和 `[4]int` 是不同类型。

## 2. 修改数组元素

数组可以通过索引访问和修改元素。

```go
package main

import "fmt"

func main() {
	var numbers = [3]int{1, 2, 3}

	numbers[0] = 10

	fmt.Println(numbers)
}
```

**注意：**索引从 `0` 开始，最后一个元素的索引是 `len(array) - 1`。

## 3. 索引

无论是数组、切片还是字符串，常见访问方式都是通过索引。

```go
package main

import "fmt"

func main() {
	sList := []string{"a", "b", "c", "d"}

	fmt.Println(sList[0])
	fmt.Println(sList[1])
	fmt.Println(sList[len(sList)-1])
	fmt.Println(sList[len(sList)-2])
}
```

对应关系如下：

```text
值：   a  b  c  d
索引： 0  1  2  3
```

Go 不支持负数索引。

```go
// 错误示例：Go 不支持这样取倒数第一个
// fmt.Println(sList[-1])
```

**重点：**想取倒数第 `n` 个元素，可以使用 `len(slice) - n`。

## 4. 切片 slice

数组长度固定，使用起来不够灵活。切片可以理解为更常用的“动态数组视图”。

切片的长度可以随着 `append` 增长。

```go
package main

import "fmt"

func main() {
	var list []string

	list = append(list, "枫枫")
	list = append(list, "知道")

	fmt.Println(list)
	fmt.Println(len(list))

	list[1] = "不知道"
	fmt.Println(list)
}
```

**注意：**`append` 不一定在原切片上直接修改，最好把返回值重新赋给原变量。

```go
list = append(list, "Go")
```

## 5. nil 切片和 make

切片只声明不赋值时，默认值是 **`nil`**。

```go
package main

import "fmt"

func main() {
	var list []string

	fmt.Println(list == nil)
	fmt.Println(len(list))
	fmt.Println(cap(list))
}
```

可以用 `make` 创建切片。

```go
make([]type, length, capacity)
```

参数含义：

- **`length`**：当前切片已经有多少个元素，可以直接通过索引访问。
- **`capacity`**：底层数组最多能容纳多少个元素，超过后会重新分配。

```go
package main

import "fmt"

func main() {
	list := make([]string, 3, 5)

	list[0] = "a"
	list[1] = "b"
	list[2] = "c"
	list = append(list, "d")

	fmt.Println(list)
	fmt.Println(len(list))
	fmt.Println(cap(list))
}
```

**注意：**`make([]string, 3, 5)` 创建后，长度已经是 `3`，所以 `list[0]` 到 `list[2]` 可以直接赋值。

## 6. 长度和容量

`len` 返回切片当前元素数量，`cap` 返回切片底层数组容量。

```go
package main

import "fmt"

func main() {
	list := make([]string, 0)
	fmt.Println(list, len(list), cap(list))
	fmt.Println(list == nil)

	list1 := make([]int, 2, 2)
	fmt.Println(list1, len(list1), cap(list1))
}
```

**重点：**空切片不一定是 `nil`。例如 `make([]string, 0)` 得到的是非 `nil` 的空切片。

## 7. 从数组得到切片

可以通过切片表达式从数组中取出一部分数据。

```go
package main

import "fmt"

func main() {
	array := [...]string{"a", "b", "c"}

	slice1 := array[:]
	slice2 := array[1:2]

	fmt.Println(slice1)
	fmt.Println(slice2)
}
```

切片表达式的规则：

```go
array[start:end]
```

- 从索引 `start` 开始。
- 切到索引 `end` 之前。
- 包含左边，不包含右边。

**重点：**`array[1:2]` 只包含索引 `1` 的元素，所以结果是 `[b]`。

## 8. 切片排序

整数切片可以用 `sort.Ints` 做升序排序。

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	list := []int{4, 5, 3, 2, 7}

	fmt.Println("排序前:", list)

	sort.Ints(list)
	fmt.Println("升序:", list)
}
```

如果要降序，可以使用 `sort.Sort`、`sort.Reverse` 和 `sort.IntSlice`。

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	list := []int{4, 5, 3, 2, 7}

	sort.Sort(sort.Reverse(sort.IntSlice(list)))

	fmt.Println("降序:", list)
}
```

这句可以拆开理解：

```go
sort.IntSlice(list)
```

把 `[]int` 转成 `sort.IntSlice` 类型。

```go
sort.Reverse(...)
```

把“小到大”的比较规则反过来。

```go
sort.Sort(...)
```

执行排序。

也可以使用 `sort.Slice` 写得更直观。

```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	list := []int{4, 5, 3, 2, 7}

	sort.Slice(list, func(i, j int) bool {
		return list[i] > list[j]
	})

	fmt.Println(list)
}
```

**重点：**`return list[i] > list[j]` 表示大的元素排在前面，所以是降序。

## 9. map

`map` 是 Go 内置的映射结构，用来保存**键值对**。

```text
key -> value
```

map 的特点：

- `map` 是**无序**的，遍历顺序不能保证。
- key 必须是**可比较类型**，例如字符串、数字、布尔值、数组等。
- value 可以是任意类型。
- 切片、map、函数不能作为 map 的 key。

**注意：**不要说 map 的 key 必须是基本数据类型，更准确的说法是 key 必须可比较。

## 10. 创建和使用 map

可以先声明，再用 `make` 初始化。

```go
package main

import "fmt"

func main() {
	var m1 map[string]string
	m1 = make(map[string]string)

	m1["name"] = "枫枫"

	fmt.Println(m1)
	fmt.Println(m1["name"])
}
```

也可以直接创建空 map。

```go
package main

import "fmt"

func main() {
	m1 := map[string]string{}
	m2 := make(map[string]string)

	m1["name"] = "枫枫"
	m2["name"] = "知道"

	fmt.Println(m1)
	fmt.Println(m2)
}
```

**注意：**只声明但没有初始化的 map 是 `nil`，不能直接写入。

```go
var m map[string]string

// 错误示例：nil map 不能直接赋值
// m["name"] = "枫枫"
```

## 11. 删除 map 中的值

使用 `delete` 删除指定 key。

```go
package main

import "fmt"

func main() {
	m1 := map[string]string{
		"name": "枫枫",
	}

	delete(m1, "name")

	fmt.Println(m1)
}
```

**注意：**删除不存在的 key 不会报错。

## 12. 判断 map 中的 key 是否存在

只接收一个返回值时，如果 key 不存在，会得到 value 类型的零值。

```go
package main

import "fmt"

func main() {
	m1 := map[string]int{
		"age": 21,
	}

	age := m1["age1"]
	fmt.Println(age)
}
```

更推荐使用两个返回值判断 key 是否存在。

```go
package main

import "fmt"

func main() {
	m1 := map[string]int{
		"age": 21,
	}

	age, ok := m1["age1"]
	fmt.Println(age, ok)
}
```

**重点：**第二个返回值通常命名为 `ok`，表示这个 key 是否真的存在。

## 13. 本节易错点

- **数组长度固定**，切片长度可以变化。
- **数组长度属于类型的一部分**，`[3]int` 和 `[4]int` 不是同一种类型。
- **Go 不支持负数索引**，倒数取值要用 `len(slice) - n`。
- **切片只声明不赋值时是 `nil`**，但 `make([]T, 0)` 得到的是非 `nil` 空切片。
- **`make` 的 length 表示已经存在的元素数量**，capacity 表示容量。
- **切片表达式左闭右开**，例如 `array[1:2]` 只取索引 `1`。
- **map 是无序的**，不要依赖遍历顺序。
- **map 的 key 必须可比较**，切片、map、函数不能作为 key。
- **nil map 不能写入数据**，写入前要先初始化。
