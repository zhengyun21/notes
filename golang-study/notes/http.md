# Go HTTP 请求、文件上传与空白标识符

## 学习要点

- `net/http` 用于创建和发送 HTTP 请求，基本步骤是：准备请求体、创建请求、设置 Header、发送请求、读取响应。
- JSON 请求体可以使用反引号原始字符串和 `strings.NewReader`，比字符串拼接更清晰。
- 图片、音频等文件上传通常使用 `multipart/form-data`，需要 `bytes.Buffer` 和 `mime/multipart.Writer`。
- 打开文件后要先判断 `err`，再执行 `defer file.Close()`。
- `_` 是空白标识符，用于丢弃不需要的返回值，但不要随意忽略 `error`。
- `tools` / function calling 只是声明模型可以调用的工具，真正的工具逻辑仍要由 Go 代码实现。

## 核心概念

### JSON POST 请求

发送 JSON 请求时，请求体可以这样写：

```go
payload := strings.NewReader(`{
	"model": "gpt-4o",
	"messages": [
		{"role": "user", "content": "你好"}
	]
}`)
```

创建请求：

```go
req, err := http.NewRequest(http.MethodPost, url, payload)
if err != nil {
	fmt.Println(err)
	return
}
```

设置 Header：

```go
req.Header.Set("Accept", "application/json")
req.Header.Set("Authorization", "Bearer sk-your-api-key")
req.Header.Set("Content-Type", "application/json")
```

发送请求并读取响应：

```go
res, err := http.DefaultClient.Do(req)
if err != nil {
	fmt.Println(err)
	return
}
defer res.Body.Close()

body, err := io.ReadAll(res.Body)
if err != nil {
	fmt.Println(err)
	return
}

fmt.Println(string(body))
```

### multipart 文件上传

上传文件时，请求体不是 JSON，而是 `multipart/form-data`：

```go
payload := &bytes.Buffer{}
writer := multipart.NewWriter(payload)
```

写入普通字段：

```go
if err := writer.WriteField("model", "gpt-4o-transcribe"); err != nil {
	fmt.Println(err)
	return
}
```

打开文件时要先检查错误：

```go
filePath := "audio.mp3"

file, err := os.Open(filePath)
if err != nil {
	fmt.Println(err)
	return
}
defer file.Close()
```

创建文件字段并复制文件内容：

```go
part, err := writer.CreateFormFile("file", filepath.Base(filePath))
if err != nil {
	fmt.Println(err)
	return
}

if _, err := io.Copy(part, file); err != nil {
	fmt.Println(err)
	return
}
```

关闭 `writer` 后再创建请求：

```go
if err := writer.Close(); err != nil {
	fmt.Println(err)
	return
}

req, err := http.NewRequest(http.MethodPost, url, payload)
if err != nil {
	fmt.Println(err)
	return
}

req.Header.Set("Content-Type", writer.FormDataContentType())
```

`writer.FormDataContentType()` 会自动生成带 `boundary` 的 Content-Type，上传文件时不要手写成普通的 `application/json`。

### 空白标识符 `_`

`_` 表示丢弃返回值。

例如 `io.Copy` 返回两个值：

```go
written, err := io.Copy(part, file)
```

如果不关心复制了多少字节，只关心错误：

```go
_, err := io.Copy(part, file)
```

如果函数只返回错误，不建议忽略：

```go
_ = writer.WriteField("model", "gpt-4o-transcribe")
```

更好的写法：

```go
if err := writer.WriteField("model", "gpt-4o-transcribe"); err != nil {
	fmt.Println(err)
	return
}
```

### function calling 工具定义

`tools` 用来告诉模型有哪些工具可以调用。例如：

```json
{
  "type": "function",
  "function": {
    "name": "get_current_weather",
    "description": "Get the current weather in a given location",
    "parameters": {
      "type": "object",
      "properties": {
        "location": {"type": "string"},
        "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
      },
      "required": ["location"]
    }
  }
}
```

这只是工具声明。真正查天气的代码，需要 Go 程序自己实现。

常见流程：

1. Go 程序把用户消息和 `tools` 发给模型。
2. 模型返回要调用的工具名和参数。
3. Go 程序执行真实函数或调用真实接口。
4. Go 程序把工具结果再发回模型。
5. 模型根据工具结果生成最终回答。

## Go 示例：上传音频并读取响应

```go
package main

import (
	"bytes"
	"fmt"
	"io"
	"mime/multipart"
	"net/http"
	"os"
	"path/filepath"
)

func main() {
	url := "https://example.com/v1/audio/transcriptions"
	filePath := "audio.mp3"

	payload := &bytes.Buffer{}
	writer := multipart.NewWriter(payload)

	file, err := os.Open(filePath)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer file.Close()

	part, err := writer.CreateFormFile("file", filepath.Base(filePath))
	if err != nil {
		fmt.Println(err)
		return
	}

	if _, err := io.Copy(part, file); err != nil {
		fmt.Println(err)
		return
	}

	if err := writer.WriteField("model", "gpt-4o-transcribe"); err != nil {
		fmt.Println(err)
		return
	}
	if err := writer.WriteField("language", "zh"); err != nil {
		fmt.Println(err)
		return
	}

	if err := writer.Close(); err != nil {
		fmt.Println(err)
		return
	}

	req, err := http.NewRequest(http.MethodPost, url, payload)
	if err != nil {
		fmt.Println(err)
		return
	}

	req.Header.Set("Accept", "application/json")
	req.Header.Set("Authorization", "Bearer sk-your-api-key")
	req.Header.Set("Content-Type", writer.FormDataContentType())

	res, err := http.DefaultClient.Do(req)
	if err != nil {
		fmt.Println(err)
		return
	}
	defer res.Body.Close()

	body, err := io.ReadAll(res.Body)
	if err != nil {
		fmt.Println(err)
		return
	}

	fmt.Println(string(body))
}
```

## 常见注意点

- 不要把真实 API Key 直接写进代码，建议从环境变量读取。
- 每次函数返回 `err` 后应尽快检查。
- `defer file.Close()` 必须放在 `os.Open` 错误检查之后。
- `writer.Close()` 必须在发送 multipart 请求前执行。
- JSON 请求使用 `Content-Type: application/json`。
- 文件上传请求使用 `writer.FormDataContentType()`。
- 可以检查 `res.StatusCode`，便于区分成功响应和错误响应。
