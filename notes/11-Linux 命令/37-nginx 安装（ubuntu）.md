# Nginx 安装（Ubuntu）

> Nginx 是高性能的 HTTP 和反向代理服务器，在 Ubuntu 上安装非常简便。

---

## 简介

![[Pasted image 20260705114740.png]]

![[Pasted image 20260705114751.png]]

Nginx 主要用途：
- **Web 服务器**：托管静态网站
- **反向代理**：将请求转发到后端应用（如 Tomcat、Node.js）
- **负载均衡**：分发请求到多个后端服务器
- **SSL/TLS 终端**：处理 HTTPS 加密解密

---

## 安装

### 1. 安装 Nginx

```bash
sudo apt update
sudo apt install -y nginx
```

### 2. 启动并设置开机自启

```bash
# 启动 Nginx
sudo systemctl start nginx

# 设置开机自启
sudo systemctl enable nginx

# 查看状态
sudo systemctl status nginx

# 停止 Nginx
sudo systemctl stop nginx

# 重启 Nginx
sudo systemctl restart nginx

# 重载配置（不中断服务）
sudo systemctl reload nginx
```

> **注意**：Nginx 安装后会自动注册为 `systemctl` 服务，服务名为 `nginx`。

---

## 配置防火墙

Nginx 默认绑定 `80` 端口（HTTP）和 `443` 端口（HTTPS），需要放行：

### UFW（Ubuntu 默认）

```bash
# 允许 HTTP 访问
sudo ufw allow 80/tcp

# 允许 HTTPS 访问
sudo ufw allow 443/tcp

# 或直接允许 Nginx（自动配置）
sudo ufw allow 'Nginx Full'

# 查看状态
sudo ufw status
```

---

## 验证安装

```bash
# 查看 Nginx 版本
nginx -v

# 测试配置文件语法
sudo nginx -t

# 查看监听端口
sudo ss -lntp | grep nginx
```

浏览器访问服务器 IP 或主机名：

```
http://192.168.88.130
http://服务器IP
```

> 默认显示 Nginx 欢迎页面，说明安装成功。`80` 是 HTTP 默认端口，访问时无需手动指定。

---

## 常用配置文件路径

| 路径 | 说明 |
|------|------|
| `/etc/nginx/nginx.conf` | 主配置文件 |
| `/etc/nginx/sites-available/` | 可用站点配置 |
| `/etc/nginx/sites-enabled/` | 启用的站点配置（软链接） |
| `/var/www/html/` | 默认网站根目录 |
| `/var/log/nginx/access.log` | 访问日志 |
| `/var/log/nginx/error.log` | 错误日志 |

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 安装 Nginx | `sudo apt install -y nginx` |
| 启动 Nginx | `sudo systemctl start nginx` |
| 开机自启 | `sudo systemctl enable nginx` |
| 重启 Nginx | `sudo systemctl restart nginx` |
| 重载配置 | `sudo systemctl reload nginx` |
| 测试配置 | `sudo nginx -t` |
| 查看状态 | `sudo systemctl status nginx` |
| 查看版本 | `nginx -v` |
| 开放防火墙 | `sudo ufw allow 80/tcp` |
| 查看日志 | `sudo tail -f /var/log/nginx/error.log` |

> **提示**：修改 Nginx 配置后，先用 `sudo nginx -t` 测试语法，确认无误后再用 `sudo systemctl reload nginx` 重载，避免配置错误导致服务中断。
