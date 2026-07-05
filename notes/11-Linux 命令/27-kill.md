# kill：终止进程

> `kill` 用于向进程发送信号，默认发送 `TERM` 信号（终止进程）。它不仅是"杀死"进程，更是一种进程间通信机制。

---

## 基本语法

```bash
kill [信号] PID
```

---

## 常用信号

| 信号值 | 信号名 | 作用 | 说明 |
|--------|--------|------|------|
| `1` | `SIGHUP` | 挂起信号 | 常用于重新加载配置文件 |
| `2` | `SIGINT` | 中断信号 | 等价于 `Ctrl + C` |
| `9` | `SIGKILL` | 强制终止 | **立即终止，不可捕获，不可忽略** |
| `15` | `SIGTERM` | 终止信号 | 默认信号，请求进程正常退出 |
| `18` | `SIGCONT` | 继续执行 | 恢复暂停的进程 |
| `19` | `SIGSTOP` | 暂停执行 | 等价于 `Ctrl + Z`，不可捕获 |
| `20` | `SIGTSTP` | 终端暂停 | 可捕获的暂停信号 |

---

## 示例

```bash
# 正常终止进程（默认发送 SIGTERM）
kill 1234

# 强制终止进程（SIGKILL，无法被忽略）
kill -9 1234
kill -SIGKILL 1234

# 优雅终止进程（同默认）
kill -15 1234
kill -SIGTERM 1234

# 重新加载配置文件（常用于 nginx 等）
kill -1 1234
kill -SIGHUP 1234

# 终止多个进程
kill 1234 1235 1236

# 使用进程名终止（通过 pgrep 获取 PID）
kill $(pgrep nginx)

# 终止所有同名进程
killall nginx

# 按用户终止进程
killall -u username
```

> **注意**：`kill -9` 是最后的手段。进程收到 `SIGKILL` 后无法执行清理操作，可能导致数据丢失或资源未释放。应优先使用 `kill`（默认 SIGTERM）给进程留出清理时间。

---

## 与 killall 和 pkill 的区别

| 命令 | 用法 | 说明 |
|------|------|------|
| `kill` | `kill PID` | 按 PID 终止 |
| `killall` | `killall 进程名` | 按进程名终止所有匹配进程 |
| `pkill` | `pkill 进程名` | 按名称模式终止，支持正则 |
| `pgrep` | `pgrep 进程名` | 只查找 PID，不终止 |

```bash
# 终止所有 nginx 进程
killall nginx

# 按正则模式终止（名字包含 "python" 的进程）
pkill python

# 查找 PID 但不终止
pgrep nginx
```

---

## 查找并终止进程的完整流程

```bash
# 1. 查找进程 PID
ps aux | grep 进程名
# 或
pgrep 进程名

# 2. 正常终止
kill PID

# 3. 如果进程仍在，强制终止
kill -9 PID
```

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 正常终止进程 | `kill PID` |
| 强制终止进程 | `kill -9 PID` |
| 重新加载配置 | `kill -1 PID` |
| 按名称终止所有 | `killall 进程名` |
| 按正则终止 | `pkill 模式` |
| 查找 PID | `pgrep 进程名` |

> **安全提示**：使用 `kill -9` 前，先用 `kill`（默认）尝试，给进程机会清理资源。某些数据库、缓存服务被 `kill -9` 后可能需要数据恢复。
