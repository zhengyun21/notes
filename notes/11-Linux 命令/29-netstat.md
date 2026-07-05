# netstat：网络连接与端口统计

> `netstat` 用于显示网络连接、路由表、接口统计、伪装连接和多播成员等信息。虽然 `ss` 命令正在逐步替代它，但 `netstat` 仍然广泛被使用。

---

## 基本语法

```bash
netstat [选项]
```

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-a` | 显示所有连接（包括监听和非监听） |
| `-t` | 只显示 TCP 连接 |
| `-u` | 只显示 UDP 连接 |
| `-l` | 只显示监听状态的端口 |
| `-n` | 以数字形式显示地址和端口（不解析域名） |
| `-p` | 显示占用端口的进程/程序名 |
| `-r` | 显示路由表 |
| `-i` | 显示网络接口统计 |
| `-s` | 显示各协议的统计摘要 |
| `-c` | 持续刷新输出 |
| `-e` | 显示扩展信息 |

---

## 常用组合

```bash
# 查看所有监听的端口（最常用）
netstat -tlnp

# 查看所有 TCP 连接
netstat -antp

# 查看所有 UDP 端口
netstat -ulnp

# 查看指定端口
netstat -anp | grep :3306

# 查看路由表
netstat -r

# 查看网络接口统计
netstat -i

# 持续刷新显示
netstat -tlnp -c 2
```

![[Pasted image 20260702153908.png]]

---

## 输出解读

### `netstat -tlnp` 输出示例

```
Proto Recv-Q Send-Q Local Address  Foreign Address  State   PID/Program name
tcp   0      0      0.0.0.0:22     0.0.0.0:*        LISTEN  1234/sshd
tcp   0      0      0.0.0.0:80     0.0.0.0:*        LISTEN  5678/nginx
```

| 字段 | 含义 |
|------|------|
| `Proto` | 协议（tcp/udp） |
| `Recv-Q` | 接收队列中未被应用程序读取的字节数 |
| `Send-Q` | 发送队列中未被远程主机确认的字节数 |
| `Local Address` | 本地地址和端口 |
| `Foreign Address` | 远程地址和端口 |
| `State` | 连接状态 |
| `PID/Program name` | 占用该端口的进程 ID 和程序名 |

### TCP 连接状态

| 状态 | 含义 |
|------|------|
| `LISTEN` | 监听中，等待连接 |
| `ESTABLISHED` | 连接已建立 |
| `TIME_WAIT` | 连接已关闭，等待足够时间以确保对方收到 ACK |
| `CLOSE_WAIT` | 对方已关闭连接，等待本地关闭 |
| `SYN_SENT` | 正在尝试建立连接 |
| `SYN_RECV` | 收到连接请求，等待确认 |
| `FIN_WAIT1` | 已发送关闭请求，等待对方确认 |
| `FIN_WAIT2` | 已收到对方关闭确认，等待对方关闭 |

---

## 与 ss 命令对比

`ss` 是 `netstat` 的现代替代品，性能更好，功能更强大。

| 需求 | netstat | ss |
|------|---------|-----|
| 查看监听端口 | `netstat -tlnp` | `ss -tlnp` |
| 查看 TCP 连接 | `netstat -antp` | `ss -antp` |
| 查看 UDP 端口 | `netstat -ulnp` | `ss -ulnp` |
| 性能 | 较慢（读取 /proc） | 更快（直接读取内核） |
| 显示更多信息 | 有限 | 支持更多过滤器和状态 |

> **推荐**：新系统优先使用 `ss` 命令，`netstat` 在很多最小化安装的系统上已不再预装。

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 查看监听端口 | `netstat -tlnp` |
| 查看所有 TCP 连接 | `netstat -antp` |
| 查看指定端口 | `netstat -anp \| grep :3306` |
| 查看路由表 | `netstat -r` 或 `ip route` |
| 持续监控 | `netstat -tlnp -c` |
| 查看接口统计 | `netstat -i` |

> **安装**：`netstat` 属于 `net-tools` 包。`sudo apt install net-tools` 或 `sudo yum install net-tools`。
