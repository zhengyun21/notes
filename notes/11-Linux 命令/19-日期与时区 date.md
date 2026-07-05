# date：日期与时区管理

> `date` 命令用于显示和设置系统日期、时间，支持多种格式化输出，在脚本中非常常用。

---

## 1. 显示当前日期时间

### 基本用法

```bash
# 显示当前日期时间（默认格式）
date

# 显示指定时区的时间
TZ="Asia/Shanghai" date
TZ="America/New_York" date
```

![[Pasted image 20260630160728.png]]

---

## 2. 格式化输出

```bash
date "+格式字符串"
```

### 常用格式符

| 格式符 | 含义 | 示例 |
|--------|------|------|
| `%Y` | 四位年份 | 2024 |
| `%m` | 两位月份 | 07 |
| `%d` | 两位日期 | 05 |
| `%H` | 24小时制小时 | 14 |
| `%M` | 分钟 | 30 |
| `%S` | 秒 | 00 |
| `%s` | Unix 时间戳（秒） | 1720166400 |
| `%A` | 星期全称 | Friday |
| `%a` | 星期简称 | Fri |
| `%B` | 月份全称 | July |
| `%b` | 月份简称 | Jul |
| `%Z` | 时区名称 | CST |
| `%z` | 时区偏移 | +0800 |
| `%j` | 一年中的第几天 | 187 |
| `%W` | 一年中的第几周 | 27 |

### 示例

```bash
# 输出：2024-07-05 14:30:00
date "+%Y-%m-%d %H:%M:%S"

# 输出：20240705（常用作文件名后缀）
date "+%Y%m%d"

# 输出：20240705_143000
date "+%Y%m%d_%H%M%S"

# 输出 Unix 时间戳
date +%s

# 输出：Friday, July 05, 2024
date "+%A, %B %d, %Y"
```

![[Pasted image 20260630163414.png]]

---

## 3. 日期加减

```bash
date -d "表达式" "+格式"
```

### 示例

```bash
# 昨天
date -d "yesterday" "+%Y-%m-%d"

# 明天
date -d "tomorrow" "+%Y-%m-%d"

# 3 天后
date -d "+3 days" "+%Y-%m-%d"

# 7 天前
date -d "-7 days" "+%Y-%m-%d"

# 1 个月前
date -d "-1 month" "+%Y-%m-%d"

# 1 小时后
date -d "+1 hour" "+%H:%M:%S"

# 获取上周一的日期
date -d "last monday" "+%Y-%m-%d"
```

![[Pasted image 20260630163509.png]]

---

## 4. 设置系统时间（需要 root 权限）

```bash
# 设置系统时间
sudo date -s "2024-07-05 14:30:00"

# 只设置日期
sudo date -s "2024-07-05"

# 只设置时间
sudo date -s "14:30:00"
```

> **建议**：生产环境推荐使用 NTP 同步时间，而不是手动设置。

---

## 5. 时区管理

```bash
# 查看当前时区
cat /etc/timezone
# 或
timedatectl

# 列出所有可用时区
timedatectl list-timezones

# 设置时区为上海
sudo timedatectl set-timezone Asia/Shanghai

# 设置时区为纽约
sudo timedatectl set-timezone America/New_York
```

---

## 6. 快速对比

| 需求 | 命令 |
|------|------|
| 查看当前时间 | `date` |
| 格式化输出日期 | `date "+%Y-%m-%d"` |
| 获取时间戳 | `date +%s` |
| 昨天 | `date -d "yesterday" "+%Y-%m-%d"` |
| 7 天后 | `date -d "+7 days" "+%Y-%m-%d"` |
| 创建日期命名文件 | `touch backup_$(date +%Y%m%d).tar.gz` |
| 设置时区 | `sudo timedatectl set-timezone Asia/Shanghai` |

> **脚本常用技巧**：`backup_$(date +%Y%m%d_%H%M%S).tar.gz` 生成带时间戳的备份文件名。
