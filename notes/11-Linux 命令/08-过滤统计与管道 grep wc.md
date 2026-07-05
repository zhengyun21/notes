# grep、wc 与管道符：过滤、统计与连接

> `grep` 是文本过滤利器，`wc` 用于统计行数/字数/字节数，管道符 `|` 能将多个命令串联成流水线。

---

## 1. grep：文本过滤

`grep` 从文件或输入流中按**正则表达式**匹配并输出行。

### 基本语法

```bash
grep [选项] "匹配模式" [文件...]
```

### 常用选项

| 选项 | 全称 | 作用 |
|------|------|------|
| `-i` | ignore-case | 忽略大小写 |
| `-v` | invert-match | **反向匹配**（输出不匹配的行） |
| `-n` | line-number | 显示行号 |
| `-c` | count | 只显示匹配行数 |
| `-r` | recursive | 递归搜索目录下所有文件 |
| `-l` | files-with-matches | 只显示包含匹配的文件名 |
| `-E` | extended-regexp | 使用扩展正则表达式 |
| `-w` | word-regexp | 只匹配完整单词 |
| `--color` |  | 高亮显示匹配内容 |

### 示例

```bash
# 在文件中搜索包含 "error" 的行
grep "error" log.txt

# 忽略大小写搜索
grep -i "error" log.txt

# 显示行号
grep -n "error" log.txt

# 反向匹配：显示不包含 "error" 的行
grep -v "error" log.txt

# 递归搜索当前目录下所有 .py 文件中的 "import"
grep -r "import" *.py

# 只匹配完整单词 "error"（不匹配 "errors"）
grep -w "error" log.txt

# 高亮显示匹配内容
grep --color "error" log.txt
```

![[Pasted image 20260628144543.png]]

---

## 2. wc：统计

`wc`（Word Count）统计文件的行数、字数和字节数。

### 基本语法

```bash
wc [选项] [文件...]
```

### 常用选项

| 选项 | 作用 |
|------|------|
| `-l` | 只统计**行数**（最常用） |
| `-w` | 只统计**单词数** |
| `-c` | 只统计**字节数** |
| `-m` | 只统计**字符数** |

### 示例

```bash
# 统计文件的总行数、单词数、字节数
wc file.txt

# 只统计行数
wc -l file.txt

# 统计多个文件
cat file1.txt file2.txt | wc -l

# 统计当前目录下有多少个文件
ls | wc -l
```

![[Pasted image 20260628144752.png]]

![[Pasted image 20260628151433.png]]

> **提示**：在日志分析中，`wc -l` 是最常用的组合，比如统计错误日志有多少条：`grep "ERROR" app.log | wc -l`。

---

## 3. 管道符 `|`：命令流水线

管道符将前一个命令的**标准输出**作为后一个命令的**标准输入**。

### 基本用法

```bash
命令1 | 命令2 | 命令3 ...
```

### 经典组合示例

```bash
# 查看日志中的错误并统计条数
grep "ERROR" /var/log/syslog | wc -l

# 查看进程列表并过滤出 nginx
ps aux | grep nginx

# 查看目录内容并按大小排序
ls -l | sort -k5 -n

# 查看文件前 10 行并过滤关键词
head -n 20 log.txt | grep "error"

# 查看端口占用并过滤出 80 端口
netstat -anp | grep ":80"

# 查看磁盘使用情况并排序
df -h | sort -k5 -r
```

![[Pasted image 20260628151827.png]]

> **核心思想**：管道让你把简单命令组合成强大的工具链。每个命令只做一件事，通过管道连接完成复杂任务。

---

## 4. 快速对比

| 需求 | 命令组合 | 说明 |
|------|----------|------|
| 搜索日志中的错误 | `grep "ERROR" log.txt` | 基础过滤 |
| 统计错误条数 | `grep "ERROR" log.txt \| wc -l` | 管道 + 统计 |
| 实时查看新增日志 | `tail -f log.txt \| grep "error"` | 实时过滤 |
| 查找文件并过滤内容 | `find . -name "*.log" -exec grep "error" {} \;` | 查找 + 搜索 |
| 统计代码行数 | `find . -name "*.py" \| xargs wc -l` | 批量统计 |

> **进阶技巧**：`grep` 家族还有 `egrep`（等价于 `grep -E`，支持扩展正则）和 `fgrep`（等价于 `grep -F`，固定字符串匹配，速度更快）。
