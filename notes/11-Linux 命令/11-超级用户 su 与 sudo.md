# su 与 sudo：用户权限切换

> Linux 是多用户操作系统，`su` 和 `sudo` 是切换用户身份、获取更高权限的两个核心命令。

---

## 1. su：切换用户（Switch User）

`su` 用于切换到另一个用户身份，如果不指定用户名，默认切换到 `root`。

### 基本语法

```bash
su [-] [用户名]
```

### 常用选项

| 选项 | 作用 |
|------|------|
| `-` | 完全切换环境（包括环境变量），等同于 `su -l` |
| 无选项 | 只切换用户身份，保留当前环境变量 |

### 示例

```bash
# 切换到 root（需要输入 root 密码）
su

# 完全切换到 root（加载 root 的环境变量）
su -

# 切换到其他用户
su - tomcat

# 切换回原来的用户
exit
```

> **区别**：`su` 只是切换用户身份，当前目录和环境变量不变；`su -` 是完全切换，等同于以该用户重新登录。

![[Pasted image 20260630131608.png]]

---

## 2. sudo：以超级用户权限执行命令

`sudo`（Superuser Do）允许普通用户以 `root`（或其他用户）权限执行特定命令，而不需要知道 root 密码。

### 基本语法

```bash
sudo [选项] 命令
```

### 常用选项

| 选项 | 作用 |
|------|------|
| `-i` | 切换到 root 用户（等价于 `su -`） |
| `-u 用户名` | 以指定用户身份执行命令 |
| `-s` | 打开一个 shell（不加载目标用户环境） |
| `-k` | 重置认证时间，下次执行需重新输入密码 |

### 示例

```bash
# 以 root 权限执行命令
sudo apt update

# 以 root 身份编辑配置文件
sudo vim /etc/nginx/nginx.conf

# 切换到 root 用户
sudo -i

# 以指定用户运行命令
sudo -u www-data python app.py
```

![[Pasted image 20260630131932.png]]

---

## 3. 为普通用户配置 sudo 认证

默认情况下，只有 root 和 sudo 组成员才能使用 `sudo`。需要把普通用户加入 sudo 组。

### 配置步骤

```bash
# 1. 切换到 root 用户
su -

# 2. 将用户加入 sudo 组（Debian/Ubuntu）
usermod -aG sudo 用户名

# 或在 CentOS/RHEL 上：
usermod -aG wheel 用户名

# 3. 退出 root，重新登录使权限生效
exit
```

### 验证 sudo 权限

```bash
# 查看当前用户属于哪些组
groups

# 测试 sudo 权限
sudo whoami
# 输出应为 root
```

![[Pasted image 20260630132043.png]]

### 手动编辑 sudo 配置文件

```bash
# 使用 visudo 编辑（自带语法检查，推荐）
sudo visudo

# 添加一行（允许用户无需密码执行 sudo）
用户名 ALL=(ALL) NOPASSWD: ALL
```

> **注意**：`visudo` 会自动检查语法错误，避免配置错误导致无法获取 root 权限。

---

## 4. su 与 sudo 对比

| 特性 | `su` | `sudo` |
|------|------|--------|
| 是否需要 root 密码 | 是 | 否（需要用户自己的密码） |
| 是否记录日志 | 否 | 是（记录在 `/var/log/auth.log` 等） |
| 权限粒度 | 完全切换用户 | 单次命令授权 |
| 安全性 | 较低 | 较高（可审计） |
| 使用场景 | 完全切换用户环境 | 临时执行高权限命令 |
| 推荐用法 | `su -` 切换到 root | `sudo 命令` 执行单条命令 |

---

## 5. 快速参考

| 需求 | 命令 |
|------|------|
| 临时执行一条 root 命令 | `sudo 命令` |
| 切换到 root 用户 | `su -` 或 `sudo -i` |
| 切换回普通用户 | `exit` |
| 给用户添加 sudo 权限 | `sudo usermod -aG sudo 用户名` |
| 查看 sudo 日志 | `sudo grep sudo /var/log/auth.log` |
