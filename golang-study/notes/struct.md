# Go 结构体、JSON 标签和基础用法

## 核心概念

- `struct` 用来定义结构体，把多个字段组合成一个自定义类型。
- 结构体字段语法通常是：`字段名 字段类型 标签`。
- 字段名首字母大写表示该字段是导出的，其他包可以访问，`encoding/json` 也通常需要导出字段才能正常编码和解码。
- 反引号里的 `` `json:"xxx"` `` 是结构体标签，用来指定 Go 字段和 JSON 字段的对应关系。
- `omitempty` 表示字段为空值时，转 JSON 会省略该字段。
- `string` 的默认零值是空字符串 `""`。
- `*string` 是字符串指针，默认零值是 `nil`，常用于表示“这个字段没有设置”。
- `[]Message` 表示 `Message` 类型的切片，也就是多个 `Message` 组成的列表。
- `Message Message` 这种写法中，前一个 `Message` 是字段名，后一个 `Message` 是类型名。
- `var resp ApifoxModel` 表示声明一个名为 `resp` 的变量，类型是 `ApifoxModel`。
- `fmt` 是 Go 标准库里的格式化输入输出包，常用 `fmt.Println` 打印内容。

## JSON 标签

示例：

```go
type Message struct {
	Content string `json:"content"`
	Role    string `json:"role"`
}
```

这里：

```go
Content string `json:"content"`
```

表示 Go 代码里的字段名是 `Content`，转成 JSON 或从 JSON 解析时，对应的 JSON 字段名是 `content`。

如果没有 JSON 标签，Go 默认会使用结构体字段名，例如 `Content`，而不是常见的 JSON 小写字段名 `content`。

## string 和 *string

普通字符串字段：

```go
Name string
```

默认值是：

```go
""
```

字符串指针字段：

```go
Name *string
```

默认值是：

```go
nil
```

示例：

```go
type User struct {
	Name *string
}

var u User
```

此时：

```go
u.Name == nil
```

如果要给 `*string` 赋值，需要先有一个字符串变量，再取它的地址：

```go
name := "张三"
u.Name = &name
```

访问指针里的字符串，需要使用 `*` 解引用：

```go
if u.Name != nil {
	fmt.Println(*u.Name)
}
```

## omitempty

示例：

```go
type Request struct {
	Model       string  `json:"model"`
	MaxTokens   *string `json:"max_tokens,omitempty"`
	Temperature *string `json:"temperature,omitempty"`
}
```

`omitempty` 表示：如果字段是空值，转成 JSON 时省略它。

对于 `*string` 来说，如果值是 `nil`，就会被省略。

例如：

```go
req := Request{
	Model: "gpt-4",
}
```

转成 JSON 时不会包含 `max_tokens` 和 `temperature`：

```json
{
  "model": "gpt-4"
}
```

## 嵌套结构体和切片

示例：

```go
type ApifoxModel struct {
	Choices []Choice `json:"choices"`
	Created string   `json:"created"`
	ID      string   `json:"id"`
	Object  string   `json:"object"`
	Usage   Usage    `json:"usage"`
}

type Choice struct {
	FinishReason string  `json:"finish_reason"`
	Index        string  `json:"index"`
	Message      Message `json:"message"`
}

type Message struct {
	Content string `json:"content"`
	Role    string `json:"role"`
}

type Usage struct {
	CompletionTokens string `json:"completion_tokens"`
	PromptTokens     string `json:"prompt_tokens"`
	TotalTokens      string `json:"total_tokens"`
}
```

说明：

- `Choices []Choice` 表示 `choices` 是一个数组或列表。
- `Usage Usage` 表示 `usage` 是一个嵌套对象。
- `Message Message` 表示字段名是 `Message`，字段类型也是 `Message` 结构体。

对应 JSON 大致是：

```json
{
  "choices": [
    {
      "finish_reason": "stop",
      "index": "0",
      "message": {
        "content": "你好",
        "role": "assistant"
      }
    }
  ],
  "created": "1710000000",
  "id": "chatcmpl_xxx",
  "object": "chat.completion",
  "usage": {
    "completion_tokens": "20",
    "prompt_tokens": "10",
    "total_tokens": "30"
  }
}
```

## 接收 JSON 解析结果

常见写法：

```go
var resp ApifoxModel
```

意思是声明一个变量：

```go
var   resp   ApifoxModel
声明   变量名  变量类型
```

它常用于接收 JSON 解析结果：

```go
err := json.Unmarshal(data, &resp)
if err != nil {
	panic(err)
}
```

这里 `&resp` 表示把 `resp` 的地址传进去，让 `json.Unmarshal` 可以把解析出来的数据写入 `resp`。

解析后可以这样访问字段：

```go
fmt.Println(resp.ID)
fmt.Println(resp.Choices[0].Message.Content)
fmt.Println(resp.Usage.TotalTokens)
```

## 完整示例

```go
package main

import (
	"encoding/json"
	"fmt"
)

type ApifoxModel struct {
	Choices []Choice `json:"choices"`
	Created string   `json:"created"`
	ID      string   `json:"id"`
	Object  string   `json:"object"`
	Usage   Usage    `json:"usage"`
}

type Choice struct {
	FinishReason string  `json:"finish_reason"`
	Index        string  `json:"index"`
	Message      Message `json:"message"`
}

type Message struct {
	Content string `json:"content"`
	Role    string `json:"role"`
}

type Usage struct {
	CompletionTokens string `json:"completion_tokens"`
	PromptTokens     string `json:"prompt_tokens"`
	TotalTokens      string `json:"total_tokens"`
}

func main() {
	data := []byte(`{
		"choices": [
			{
				"finish_reason": "stop",
				"index": "0",
				"message": {
					"content": "你好",
					"role": "assistant"
				}
			}
		],
		"created": "1710000000",
		"id": "chatcmpl_xxx",
		"object": "chat.completion",
		"usage": {
			"completion_tokens": "20",
			"prompt_tokens": "10",
			"total_tokens": "30"
		}
	}`)

	var resp ApifoxModel

	err := json.Unmarshal(data, &resp)
	if err != nil {
		panic(err)
	}

	fmt.Println(resp.ID)
	fmt.Println(resp.Choices[0].Message.Content)
	fmt.Println(resp.Usage.TotalTokens)
}
```

## 常见注意点

- 如果 JSON 里是数字，Go 字段类型应该优先用 `int`、`int64` 或 `float64`，不要随便写成 `string`。
- 如果 JSON 里是字符串，例如 `"20"`，Go 字段才适合用 `string`。
- 使用 `*string` 前要判断是否为 `nil`，否则直接 `*变量名` 可能导致运行时错误。
- `omitempty` 只影响编码成 JSON 时是否省略字段，不代表字段不存在时会自动填默认值。
- 访问切片元素前要确认切片里有数据，例如访问 `resp.Choices[0]` 前最好判断 `len(resp.Choices) > 0`。
- `fmt.Println` 来自标准库 `fmt`，使用前需要写 `import "fmt"`。
