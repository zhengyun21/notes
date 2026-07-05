# MySQL 8 设置远程连接

> MySQL 8 默认只允许本地连接。要启用远程访问，需要修改配置文件、创建远程用户、并开放防火墙端口。

---

## 1. 修改 MySQL 监听地址

编辑 MySQL 配置文件，将绑定地址从 `127.0.0.1` 改为 `0.0.0.0`：

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
# 或
sudo nano /etc/my.cnf
```

找到并修改：

```ini
# 原配置（仅本地）
bind-address = 127.0.0.1

# 改为（监听所有网卡）
bind-address = 0.0.0.0
```

| 地址 | 含义 |
|------|------|
| `127.0.0.1` | 仅允许本地连接 |
| `0.0.0.0` | 监听所有网卡，允许远程访问 |
| `192.168.x.x` | 仅监听指定内网 IP |

重启 MySQL 生效：

```bash
sudo systemctl restart mysql
```

---

## 2. 创建允许远程登录的 MySQL 用户

进入 MySQL：

```bash
sudo mysql
# 或
mysql -u root -p
```

### 创建远程用户（推荐）

```sql
-- 允许任意 IP 连接（% 表示任意主机）
CREATE USER 'myuser'@'%' IDENTIFIED BY '强密码';
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;

-- 只允许指定 IP 连接（更安全）
CREATE USER 'myuser'@'192.168.1.100' IDENTIFIED BY '强密码';
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.100';
FLUSH PRIVILEGES;
```

> **安全提醒**：不要开放 `root` 用户的远程登录！始终创建独立的远程用户，并限制来源 IP。

### 修改已有用户

```sql
-- 修改密码
ALTER USER 'myuser'@'%' IDENTIFIED BY '新密码';

-- 重新授权
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%';
FLUSH PRIVILEGES;

-- 查看所有用户及允许来源
SELECT user, host FROM mysql.user;

-- 查看用户权限
SHOW GRANTS FOR 'myuser'@'%';
```

---

## 3. 开放防火墙端口

### UFW（Ubuntu）

```bash
# 开放 3306 端口（所有 IP）
sudo ufw allow 3306/tcp

-- 更安全：只允许指定 IP
sudo ufw allow from 192.168.1.100 to any port 3306 proto tcp

-- 重载防火墙
sudo ufw reload

-- 查看状态
sudo ufw status
```

### firewalld（CentOS）

```bash
sudo firewall-cmd --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
```

---

## 4. 验证远程连接

### 服务端验证监听状态

```bash
sudo ss -lntp | grep 3306

-- 正确输出示例（0.0.0.0 表示监听所有地址）：
-- LISTEN 0 151 0.0.0.0:3306 0.0.0.0:* users:(("mysqld",pid=xxxx,fd=xx))
```

> 如果显示 `127.0.0.1:3306`，说明配置未生效，检查 `bind-address` 并重启 MySQL。

### 客户端测试连接

```bash
mysql -h 服务器IP -P 3306 -u myuser -p

-- 示例
mysql -h 192.168.1.10 -P 3306 -u myuser -p
```

---

## 5. 常见问题排查

| 错误 | 原因 | 解决 |
|------|------|------|
| `Host is not allowed to connect` | MySQL 用户 `host` 不匹配 | 创建 `%` 或指定 IP 的用户 |
| `Access denied for user` | 密码错误或用户不存在 | 检查用户名、密码、来源 host |
| `Can't connect to MySQL server` | 防火墙或网络不通 | 检查防火墙、MySQL 是否运行 |
| `Connection refused` | MySQL 未监听远程地址 | 检查 `bind-address` 配置 |
| 客户端连接失败（MySQL 8） | 认证插件不兼容 | 改用 `mysql_native_password` |

### MySQL 8 认证插件问题

MySQL 8 默认使用 `caching_sha2_password`，部分旧客户端不支持。可以改用 `mysql_native_password`：

```sql
ALTER USER 'myuser'@'%' IDENTIFIED WITH mysql_native_password BY '密码';
FLUSH PRIVILEGES;
```

---

## 6. 安全建议

- ❌ 不要开放 `root` 远程登录
- ❌ 不要将 `3306` 直接暴露到公网
- ✅ 限制来源 IP（只允许内网/VPN/办公网）
- ✅ 使用独立业务用户，只授予必要权限
- ✅ 配合云安全组、防火墙、强密码策略
- ✅ 生产环境建议启用 SSL 连接

---

## 7. 快速命令汇总

```bash
-- 编辑配置
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf

-- 重启 MySQL
sudo systemctl restart mysql

-- 开放防火墙
sudo ufw allow from 192.168.1.0/24 to any port 3306

-- 验证监听
sudo ss -lntp | grep 3306

-- 客户端连接测试
mysql -h 192.168.1.10 -P 3306 -u myuser -p
```

```sql
-- MySQL 中执行
CREATE USER 'myuser'@'%' IDENTIFIED BY '强密码';
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SELECT user, host FROM mysql.user;
SHOW GRANTS FOR 'myuser'@'%';
```
