# curl：网络请求与数据传输

> `curl` 是功能强大的命令行数据传输工具，支持多种协议（HTTP、HTTPS、FTP、SFTP 等），是 API 调试、文件下载、上传的首选工具。

---

## 基本语法

```bash
curl [选项] URL
```

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-o 文件名` | 将输出保存到文件（指定文件名） |
| `-O` | 使用远程文件名保存（大写字母 O） |
| `-L` | 跟随重定向（Location） |
| `-I` | 仅获取响应头（HEAD 请求） |
| `-v` | 显示详细通信过程 |
| `-s` | 静默模式（不显示进度条） |
| `-S` | 显示错误（配合 -s 使用：`-sS`） |
| `-f` | 失败时返回 HTTP 错误码而非页面内容 |
| `-X 方法` | 指定 HTTP 方法（GET/POST/PUT/DELETE 等） |
| `-H "Header: Value"` | 添加自定义请求头 |
| `-d "数据"` | 发送 POST 数据 |
| `-F "name=@file"` | 上传文件（multipart/form-data） |
| `-u 用户名:密码` | 基本认证（Basic Auth） |
| `-k` | 忽略 SSL 证书验证 |
| `--connect-timeout 秒` | 连接超时时间 |
| `--max-time 秒` | 最大传输时间 |
| `-C -` | 断点续传 |
| `-A "User-Agent"` | 设置 User-Agent |
| `-e "URL"` | 设置 Referer |
| `-b "Cookie"` | 发送 Cookie |
| `-c 文件` | 保存 Cookie 到文件 |
| `-T 文件` | 上传文件到服务器 |

---

## 示例

### 下载文件

```bash
# 下载文件并保存到 stdout（屏幕）
curl https://example.com/file.txt

# 下载并保存到指定文件
curl -o myfile.txt https://example.com/file.txt

# 下载并用远程文件名保存
curl -O https://example.com/file.txt

# 断点续传
curl -C - -O https://example.com/largefile.zip

# 跟随重定向下载
curl -L -o file.zip https://example.com/redirect
```

![[Pasted image 20260702152306.png]]

### 查看响应头

```bash
# 只获取响应头（HEAD 请求）
curl -I https://www.baidu.com

# 获取完整请求和响应过程
curl -v https://www.baidu.com
```

### HTTP 请求方法

```bash
# GET 请求（默认）
curl https://api.example.com/users

# POST 请求（发送 JSON）
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"name":"tom","age":20}' \
  https://api.example.com/users

# PUT 请求
curl -X PUT \
  -H "Content-Type: application/json" \
  -d '{"name":"jerry"}' \
  https://api.example.com/users/1

# DELETE 请求
curl -X DELETE https://api.example.com/users/1
```

### 上传文件

```bash
# 上传文件（multipart/form-data）
curl -F "file=@/path/to/local.zip" https://api.example.com/upload

# 上传文件到 FTP
curl -T localfile.txt ftp://ftp.example.com/ --user username:password
```

### 认证

```bash
# 基本认证
curl -u username:password https://api.example.com/data

# Bearer Token
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com/data
```

---

## 常用组合

```bash
# 静默下载，只显示错误
curl -sS -o file.zip https://example.com/file.zip

# 测试 API 接口并格式化 JSON 输出
curl -s https://api.github.com/users/github | python3 -m json.tool

# 下载并显示进度
curl -# -o file.zip https://example.com/file.zip

# 查看公网 IP
curl ifconfig.me
```

---

## 与 wget 的区别

| 特性 | curl | wget |
|------|------|------|
| 设计目标 | 数据传输与 API 请求 | 文件下载 |
| 默认输出 | stdout | 保存为文件 |
| 支持 HTTP 方法 | 全部 | 主要是 GET |
| 上传文件 | ✅ 支持 | ⚠️ 有限支持 |
| 自定义请求头 | ✅ 支持 | ❌ 不支持 |
| 递归下载 | ❌ 不支持 | ✅ 支持 |
| 后台下载 | ❌ 不支持 | ✅ 支持 |
| 断点续传 | ✅ 支持 | ✅ 支持 |

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 下载文件 | `curl -O URL` 或 `curl -o 文件名 URL` |
| 查看响应头 | `curl -I URL` |
| 跟随重定向 | `curl -L URL` |
| POST JSON 数据 | `curl -X POST -H "Content-Type: application/json" -d '{"key":"val"}' URL` |
| 上传文件 | `curl -F "file=@/path" URL` |
| 基本认证 | `curl -u user:pass URL` |
| 忽略 SSL 证书 | `curl -k URL` |
| 查看公网 IP | `curl ifconfig.me` |

> **推荐**：`curl` 是测试 REST API 的神器，配合 `-v` 可以查看完整请求和响应过程，排查网络问题非常高效。
