# systemctl：系统服务管理

> `systemctl` 是 systemd 系统的核心管理工具，用于控制（启动、停止、重启）服务，设置开机自启，查看服务状态等。

---

## 基本语法

```bash
systemctl [选项] 命令 [服务名]
```

---

## 服务控制命令

| 命令 | 作用 |
|------|------|
| `start 服务名` | 启动服务 |
| `stop 服务名` | 停止服务 |
| `restart 服务名` | 重启服务（先 stop 再 start） |
| `reload 服务名` | 重新加载配置文件（不中断服务） |
| `status 服务名` | 查看服务状态 |
| `enable 服务名` | 设置开机自启 |
| `disable 服务名` | 关闭开机自启 |
| `is-enabled 服务名` | 检查是否已设置开机自启 |
| `is-active 服务名` | 检查服务是否正在运行 |
| `is-failed 服务名` | 检查服务是否启动失败 |

---

## 示例

```bash
# 启动 nginx 服务
sudo systemctl start nginx

# 停止 nginx 服务
sudo systemctl stop nginx

# 重启 nginx 服务
sudo systemctl restart nginx

# 重载 nginx 配置（不中断服务）
sudo systemctl reload nginx

# 查看 nginx 状态
sudo systemctl status nginx

# 设置 nginx 开机自启
sudo systemctl enable nginx

# 关闭 nginx 开机自启
sudo systemctl disable nginx

# 检查 nginx 是否已启用
sudo systemctl is-enabled nginx
```

![[Pasted image 20260630153736.png]]

---

## 查看所有服务

```bash
# 列出所有已加载的服务单元
systemctl list-units --type=service

# 列出所有正在运行的服务
systemctl list-units --type=service --state=running

# 列出所有失败的服务
systemctl --failed
```

---

## 常用对比

| 需求 | 命令 |
|------|------|
| 启动服务 | `sudo systemctl start nginx` |
| 停止服务 | `sudo systemctl stop nginx` |
| 重启服务 | `sudo systemctl restart nginx` |
| 重载配置 | `sudo systemctl reload nginx` |
| 查看状态 | `sudo systemctl status nginx` |
| 开机自启 | `sudo systemctl enable nginx` |
| 关闭自启 | `sudo systemctl disable nginx` |
| 查看所有服务 | `systemctl list-units --type=service` |

> **提示**：在 CentOS 7 之前使用 `service` 命令（如 `service nginx start`），CentOS 7 及以后推荐使用 `systemctl`。部分系统同时支持两者。
