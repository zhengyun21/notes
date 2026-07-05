# wget：文件下载工具

> `wget` 是 Linux 中功能强大的非交互式文件下载工具，支持 HTTP、HTTPS、FTP 协议，支持断点续传和后台下载。

---

## 基本语法

```bash
wget [选项] URL
```

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-O 文件名` | 指定下载后的文件名 |
| `-P 目录` | 指定下载保存目录 |
| `-c` | **断点续传**（continue），继续未完成的下载 |
| `-b` | **后台下载**（background），下载日志写入 wget-log |
| `-q` | 静默模式（quiet），不显示输出 |
| `--no-check-certificate` | 不检查 HTTPS 证书 |
| `-t 次数` | 设置重试次数 |
| `-T 秒数` | 设置超时时间 |
| `-r` | 递归下载（下载整个网站） |
| `-l 层级` | 递归深度限制 |
| `-np` | 不向上级目录递归 |
| `-nd` | 不创建目录层次，所有文件下载到当前目录 |
| `-m` | 镜像网站（等价于 `-r -N -l inf --no-remove-listing`） |
| `--limit-rate=速度` | 限制下载速度 |
| `-U "User-Agent"` | 设置 User-Agent |

---

## 示例

```bash
# 基本下载
wget https://example.com/file.zip

# 指定文件名
wget -O myfile.zip https://example.com/file.zip

# 指定保存目录
wget -P /downloads/ https://example.com/file.zip

# 断点续传
wget -c https://example.com/largefile.iso

# 后台下载
wget -b https://example.com/file.zip
# 查看下载进度
tail -f wget-log

# 不检查 HTTPS 证书（自签名证书场景）
wget --no-check-certificate https://example.com/file.zip

# 限速下载
wget --limit-rate=500k https://example.com/file.zip

# 递归下载网站（慎用！）
wget -r -l 2 -np https://example.com/docs/

# 下载整个网站镜像
wget -m https://example.com/
```

![[Pasted image 20260702151204.png]]

---

## 后台下载管理

```bash
# 1. 启动后台下载
wget -b https://example.com/largefile.iso

# 2. 查看下载日志（实时刷新）
tail -f wget-log

# 3. 查看 wget 进程
ps aux | grep wget

# 4. 停止下载（发送终止信号）
kill 进程ID

# 5. 强制停止
kill -9 进程ID

# 6. 重新下载（断点续传）
wget -c https://example.com/largefile.iso
```

> **注意**：`kill -9` 是强制终止，如果文件下载了一半，下次用 `-c` 续传时可能会出问题。尽量先用普通 `kill` 让 wget 优雅退出。

---

## 与 curl 的区别

| 特性 | wget | curl |
|------|------|------|
| 主要用途 | 下载文件 | 发送 HTTP 请求 |
| 递归下载 | ✅ 支持 | ❌ 不支持 |
| 断点续传 | ✅ 支持 | ✅ 支持（`-C -`） |
| 后台下载 | ✅ 支持 | ❌ 不支持 |
| 镜像网站 | ✅ 支持 | ❌ 不支持 |
| 请求方法 | 主要是 GET | 支持 GET/POST/PUT/DELETE 等 |
| 输出处理 | 直接保存为文件 | 默认输出到 stdout |

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 下载文件 | `wget URL` |
| 指定文件名 | `wget -O 文件名 URL` |
| 断点续传 | `wget -c URL` |
| 后台下载 | `wget -b URL` |
| 查看后台进度 | `tail -f wget-log` |
| 下载到指定目录 | `wget -P /目录/ URL` |
| 忽略证书错误 | `wget --no-check-certificate URL` |
| 限速下载 | `wget --limit-rate=500k URL` |

> **推荐**：大文件下载一定要用 `-c` 参数，防止网络中断后重新下载。后台下载配合 `-b` 和 `tail -f wget-log` 是服务器下载的常用组合。
