# ping：网络连通性测试

> `ping` 是最基础的网络诊断工具，用于测试主机之间的网络连通性和延迟。

---

## 基本语法

```bash
ping [选项] 目标地址
```

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-c 次数` | 发送指定次数后停止（count） |
| `-i 秒数` | 设置发送间隔（默认1秒） |
| `-s 字节数` | 设置发送数据包大小（默认56字节） |
| `-t` | 设置 TTL（生存时间） |
| `-W 秒数` | 设置等待响应的超时时间 |
| `-4` | 强制使用 IPv4 |
| `-6` | 强制使用 IPv6 |
| `-I 网卡` | 指定发送网卡 |

---

## 示例

```bash
# 测试到百度的连通性（Linux 默认持续发送，按 Ctrl+C 停止）
ping www.baidu.com

# 只发送 4 次
ping -c 4 www.baidu.com

# 测试到本地网关
ping -c 4 192.168.1.1

# 测试到指定 IP
ping -c 4 8.8.8.8

# 设置超时时间
ping -c 4 -W 2 www.baidu.com

# 使用指定网卡发送
ping -c 4 -I eth0 www.baidu.com
```

![[Pasted image 20260702150753.png]]

---

## 输出解读

```
64 bytes from 142.250.80.100: icmp_seq=1 ttl=117 time=24.3 ms
```

| 字段 | 含义 |
|------|------|
| `64 bytes` | 数据包大小 |
| `142.250.80.100` | 目标 IP 地址 |
| `icmp_seq=1` | ICMP 序列号 |
| `ttl=117` | 生存时间（Time To Live），每经过一个路由器减 1 |
| `time=24.3 ms` | 往返延迟 |

### 结尾统计

```
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 24.234/26.456/28.901/1.812 ms
```

| 字段 | 含义 |
|------|------|
| `transmitted` | 发送的数据包数 |
| `received` | 接收到的数据包数 |
| `packet loss` | 丢包率 |
| `min/avg/max/mdev` | 最小/平均/最大/标准差延迟 |

---

## 常见结果与排查

| 结果 | 含义 | 排查方向 |
|------|------|----------|
| 正常回复 | 网络连通 | 无问题 |
| `Request timeout` | 请求超时 | 目标防火墙、网络不通 |
| `Destination Host Unreachable` | 目标不可达 | 路由问题、目标关机 |
| `Unknown host` | 域名解析失败 | DNS 配置问题 |
| `Network is unreachable` | 网络不可达 | 本地网卡/路由配置错误 |

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 测试连通性 | `ping 目标` |
| 指定次数测试 | `ping -c 4 目标` |
| 测试本地网关 | `ping 192.168.1.1` |
| 测试外网 | `ping 8.8.8.8` |
| 测试 DNS | `ping www.baidu.com` |
| 停止 ping | `Ctrl + C` |

> **注意**：有些服务器会禁用 ICMP 响应（`ping`），ping 不通不代表服务不可用。此时应使用 `telnet` 或 `nc` 测试具体端口。
