# which 与 find：查找命令与文件

> `which` 用于查找命令的位置，`find` 用于按各种条件在文件系统中搜索文件。

---

## 1. which：查找命令的位置

`which` 在系统的 `PATH` 环境变量指定的目录中查找可执行文件的位置。

### 基本语法

```bash
which 命令名
```

### 示例

```bash
# 查看 python 命令在哪里
which python

# 查看多个命令
which python pip git

# 查看命令的完整路径（包括别名）
which -a python
```

> **输出示例**：`/usr/bin/python`，说明 `python` 命令实际指向这个文件。

---

## 2. find：按条件查找文件

`find` 是 Linux 中最强大的文件搜索工具，支持按名称、大小、类型、时间等多种条件搜索。

### 基本语法

```bash
find [搜索路径] [选项] [条件] [动作]
```

> 默认路径为当前目录 `.`，默认动作为 `-print`（输出文件名）。

---

### 2.1 按文件名查找

```bash
# 在当前目录下查找名为 "app.py" 的文件
find . -name "app.py"

# 忽略大小写查找
find . -iname "APP.py"

# 查找所有 .txt 文件
find . -name "*.txt"
```

![[Pasted image 20260628124434.png]]

---

### 2.2 使用通配符查找

```bash
# 查找所有以 "log" 开头的文件
find . -name "log*"

# 查找所有以数字结尾的文件
find . -name "*[0-9].txt"

# 查找包含 "config" 的文件名
find . -name "*config*"
```

![[Pasted image 20260628124612.png]]

> **注意**：使用通配符时务必加引号 `""`，防止 shell 提前展开。

---

### 2.3 按文件大小查找

```bash
# 查找大于 100MB 的文件
find . -size +100M

# 查找小于 1KB 的文件
find . -size -1k

# 查找大小在 10MB 到 100MB 之间的文件
find . -size +10M -size -100M

# 查找空文件
find . -size 0
```

常用大小单位：

| 单位 | 含义 |
|------|------|
| `b` | 块（512字节） |
| `c` | 字节 |
| `k` | KB（1024字节） |
| `M` | MB |
| `G` | GB |

![[Pasted image 20260628124838.png]]

---

### 2.4 其他常用条件

| 条件 | 作用 | 示例 |
|------|------|------|
| `-type f` | 只查找文件 | `find . -type f` |
| `-type d` | 只查找目录 | `find . -type d` |
| `-mtime -7` | 7天内修改过 | `find . -mtime -7` |
| `-mtime +30` | 30天前修改过 | `find . -mtime +30` |
| `-user 用户名` | 按所有者查找 | `find . -user root` |
| `-perm 644` | 按权限查找 | `find . -perm 644` |

---

### 2.5 对找到的文件执行动作

```bash
# 查找并删除（谨慎使用！）
find . -name "*.log" -delete

# 查找并执行命令（{} 代表找到的文件）
find . -name "*.log" -exec rm -f {} \;

# 查找并查看详细信息
find . -name "*.py" -ls

# 查找并复制到备份目录
find . -name "*.conf" -exec cp {} /backup/ \;
```

---

## 3. 快速对比

| 需求 | 命令 | 场景 |
|------|------|------|
| 查找命令位置 | `which` | 想知道某个命令安装在哪 |
| 按名称找文件 | `find -name` | 在目录树中搜索 |
| 按大小找文件 | `find -size` | 清理大文件或查找空文件 |
| 快速模糊搜索 | `find -name "*关键词*"` | 记不住完整文件名时 |
| 定位命令文件 | `whereis` | 查找命令的二进制、手册和源码 |
| 更快速搜索 | `locate` | 基于数据库搜索，速度极快（需安装 mlocate） |

> **提示**：`locate` 命令比 `find` 快得多，但依赖每天更新的数据库。安装方法：`yum install mlocate` 或 `apt install mlocate`，然后执行 `updatedb` 更新数据库。
