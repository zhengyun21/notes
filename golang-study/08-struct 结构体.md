# 08 - struct 结构体

## 1. 定义结构体

结构体用于把多个字段组合成一个新类型。

```go
type 结构体名称 struct {
	字段名 类型
}
```

示例：

```go
type Student struct {
	Name string
	Age  int
}
```

**重点：**结构体字段名首字母大写，才能被包外访问。

## 2. 创建和修改结构体

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func main() {
	s := Student{
		Name: "枫枫",
		Age:  21,
	}

	s.Name = "枫枫知道"

	fmt.Println(s.Name, s.Age)
}
```

## 3. 给结构体绑定方法

方法就是带接收者的函数。

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func (s Student) PrintInfo() {
	fmt.Printf("name:%s age:%d\n", s.Name, s.Age)
}

func main() {
	s := Student{Name: "枫枫", Age: 21}
	s.PrintInfo()
}
```

**重点：**`func (s Student) PrintInfo()` 表示给 `Student` 绑定方法。

## 4. 值接收者

值接收者会拷贝一份结构体，方法里修改不会影响原对象。

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func (s Student) SetAge(age int) {
	s.Age = age
}

func main() {
	s := Student{Name: "枫枫", Age: 21}

	s.SetAge(18)

	fmt.Println(s.Age)
}
```

输出仍然是：

```text
21
```

## 5. 指针接收者

指针接收者可以修改原对象。

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func (s *Student) SetAge(age int) {
	s.Age = age
}

func main() {
	s := Student{Name: "枫枫", Age: 21}

	s.SetAge(18)

	fmt.Println(s.Age)
}
```

输出：

```text
18
```

**重点：**需要修改结构体字段时，优先用指针接收者。

## 6. 函数中修改结构体

普通函数也一样：值传递改不了原对象，传指针才能修改。

```go
package main

import "fmt"

type Student struct {
	Name string
	Age  int
}

func SetAge(s Student, age int) {
	s.Age = age
}

func SetAgeByPointer(s *Student, age int) {
	s.Age = age
}

func main() {
	s := Student{Name: "枫枫", Age: 21}

	SetAge(s, 18)
	fmt.Println(s.Age)

	SetAgeByPointer(&s, 17)
	fmt.Println(s.Age)
}
```

输出：

```text
21
17
```

## 7. 结构体嵌入

Go 没有传统面向对象里的继承。常见写法是**结构体嵌入**。

```go
package main

import "fmt"

type People struct {
	Time string
}

func (p People) Info() {
	fmt.Println("people", p.Time)
}

type Student struct {
	People
	Name string
	Age  int
}

func main() {
	s := Student{
		People: People{Time: "2026-07-08"},
		Name:   "zzy",
		Age:    18,
	}

	s.Info()
	fmt.Println(s.People.Time)
	fmt.Println(s.Time)
}
```

**重点：**嵌入字段的方法和字段可以被提升，所以能写 `s.Info()`、`s.Time`。

## 8. 字段名冲突

如果嵌入字段和结构体本身字段重名，优先访问外层字段。

```go
type TimeInfo struct {
	Time string
}

type Student struct {
	TimeInfo
	Time string
}
```

访问嵌入字段时写完整路径：

```go
s.TimeInfo.Time
```

## 9. 结构体 tag

结构体 tag 是字段上的元信息，常给 JSON、表单、数据库映射等库使用。

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Student struct {
	Name string `json:"name"`
	Age  int    `json:"age,omitempty"`
}

func main() {
	s := Student{Name: "枫枫", Age: 21}

	data, _ := json.Marshal(s)
	fmt.Println(string(data))
}
```

输出：

```json
{"name":"枫枫","age":21}
```

**注意：**tag 里的逗号要用英文逗号：`json:"age,omitempty"`。

## 10. json tag 常见规则

```go
type User struct {
	Name     string `json:"name"`
	Age      int    `json:"age,omitempty"`
	Password string `json:"-"`
}
```

规则：

- 不写 `json` tag 时，默认使用字段名。
- 小写字段不会被 `encoding/json` 导出。
- `json:"-"` 表示不输出这个字段。
- `omitempty` 表示字段为零值时不输出。

**重点：**`omitempty` 只影响 JSON 输出，不会删除结构体字段。

## 11. 常见 tag

结构体 tag 没有固定标准，是否生效取决于对应库是否解析它。

```go
type User struct {
	Name string `json:"name" xml:"name" form:"name"`
	Age  int    `json:"age" db:"age_col" gorm:"column:age_col"`
}
```

常见用途：

- `json`：给 `encoding/json` 用。
- `xml`：给 `encoding/xml` 用。
- `form`：给表单绑定用。
- `db`：给数据库映射用。
- `gorm`：给 GORM 用。
- `bson`：给 MongoDB 用。
- `validate` / `binding`：给校验库用。

## 12. 易错点

- **结构体字段名大写才可导出**。
- **值接收者改不了原结构体**。
- **指针接收者可以修改原结构体**。
- **Go 没有传统继承，常用结构体嵌入组合能力**。
- **嵌入字段可被提升访问**。
- **JSON 只会处理导出字段**。
- **tag 逗号必须是英文逗号**。
- **`omitempty` 是零值不输出，不是删除字段**。
