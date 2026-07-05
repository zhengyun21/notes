# sar：系统活动报告

> `sar`（System Activity Reporter）是 `sysstat` 工具包中的核心命令，用于收集、报告和保存系统的各种活动信息，是历史性能分析的强大工具。

---

## 基本语法

```bash
sar [选项] [间隔] [次数]
```

---

## 常用选项

| 选项 | 监控内容 |
|------|----------|
| `-u` | CPU 使用率 |
| `-r` | 内存和交换空间使用率 |
| `-b` | I/O 和传输速率 |
| `-d` | 块设备活动 |
| `-n DEV` | 网络接口统计 |
| `-n SOCK` | Socket 统计 |
| `-q` | 队列长度和负载 |
| `-w` | 任务创建和系统切换 |
| `-W` | 交换空间活动 |
| `-S` | 交换空间使用率 |
| `-v` | 文件、inode、进程表状态 |
| `-P ALL` | 每个 CPU 核心的统计 |
| `-f 文件` | 从指定的数据文件读取 |

---

## 安装 sysstat

`sar` 属于 `sysstat` 包，通常需要手动安装：

```bash
# Ubuntu/Debian
sudo apt install sysstat

# CentOS/RHEL
sudo yum install sysstat

# 启用数据收集（编辑配置文件）
sudo vim /etc/default/sysstat   # Ubuntu
sudo vim /etc/sysconfig/sysstat # CentOS
# 将 ENABLED="false" 改为 ENABLED="true"

# 启动并启用服务
sudo systemctl start sysstat
sudo systemctl enable sysstat
```

---

## 示例

```bash
# 查看 CPU 使用率（每2秒采样一次，共采样3次）
sar -u 2 3

# 查看内存使用率
sar -r 2 3

# 查看 I/O 统计
sar -b 2 3

# 查看块设备活动
sar -d 2 3

# 查看网络接口统计
sar -n DEV 2 3

# 查看每个 CPU 核心
sar -P ALL 1 3

# 查看队列和负载
sar -q 1 5

# 查看历史数据（从 /var/log/sysstat/ 读取）
sar -u -f /var/log/sysstat/sa01

# 查看昨天的 CPU 数据
sar -u -f /var/log/sysstat/sa$(date -d yesterday +%d)

# 查看今天到目前为止的所有 CPU 数据
sar -u
```

---

## 输出解读

### `sar -u` CPU 使用率

```
CPU     %user   %nice   %system  %iowait  %steal   %idle
all     15.23   0.00    5.67     0.50     0.00     78.60
```

| 字段 | 含义 |
|------|------|
| `%user` | 用户空间占用 CPU |
| `%nice` | 带 nice 优先级的用户空间 CPU |
| `%system` | 内核空间占用 CPU |
| `%iowait` | 等待 I/O 的 CPU 时间 |
| `%steal` | 被虚拟机偷走的 CPU 时间 |
| `%idle` | 空闲 CPU |

### `sar -r` 内存统计

| 字段 | 含义 |
|------|------|
| `kbmemfree` | 空闲内存 |
| `kbmemused` | 已用内存 |
| `kbbuffers` | 缓冲区 |
| `kbcached` | 缓存 |
| `kbswpfree` | 空闲交换空间 |
| `kbswpused` | 已用交换空间 |

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 实时查看 CPU | `sar -u 2 3` |
| 实时查看内存 | `sar -r 2 3` |
| 实时查看 I/O | `sar -b 2 3` |
| 查看网络接口 | `sar -n DEV 2 3` |
| 查看每个 CPU | `sar -P ALL 1 3` |
| 查看历史数据 | `sar -u -f /var/log/sysstat/sa01` |
| 查看负载 | `sar -q` |

---

## 与 top / iostat 的对比

| 工具 | 特点 | 场景 |
|------|------|------|
| `top` | 实时、交互式 | 实时排查 |
| `iostat` | 实时 I/O 统计 | 磁盘瓶颈分析 |
| `sar` | 历史 + 实时、可保存 | 长期趋势分析、历史回溯 |

> **优势**：`sar` 最大的优势是可以保存历史数据到 `/var/log/sysstat/`，事后可以回看任意时间点的系统状态。这对于分析"昨晚服务器为什么卡了"这类问题非常有价值。
