# cp、mv、rm：复制、移动、删除

> 这是 Linux 文件管理中最核心的三个命令：复制（cp）、移动/重命名（mv）、删除（rm）。

---

## 1. cp：复制文件或目录

### 基本语法

```bash
cp [选项] 源文件 目标文件
cp [选项] 源文件... 目标目录
```

### 常用选项

| 选项 | 全称 | 作用 |
|------|------|------|
| `-r` | recursive | **递归复制**目录及其内容 |
| `-i` | interactive | 覆盖前提示确认 |
| `-f` | force | 强制覆盖，不提示 |
| `-v` | verbose | 显示复制过程 |
| `-p` | preserve | 保留文件属性（权限、时间戳等） |
| `-a` | archive | 归档模式（相当于 `-dR --preserve=all`），常用于备份 |

### 示例

```bash
# 复制文件
cp file.txt backup.txt

# 复制文件到目录
cp file.txt /backup/

# 递归复制目录
cp -r mydir /backup/

# 复制并保留属性
cp -a source_dir/ dest_dir/

# 复制多个文件到目录
cp file1.txt file2.txt /backup/
```

![[Pasted image 20260628122144.png]]

---

## 2. mv：移动或重命名

### 基本语法

```bash
mv [选项] 源文件 目标文件
mv [选项] 源文件... 目标目录
```

### 常用选项

| 选项 | 作用 |
|------|------|
| `-i` | 覆盖前提示确认 |
| `-f` | 强制覆盖 |
| `-v` | 显示移动过程 |
| `-n` | 不覆盖已有文件 |

### 示例

```bash
# 重命名文件
mv oldname.txt newname.txt

# 移动文件到目录
mv file.txt /backup/

# 移动并重命名
mv file.txt /backup/newname.txt

# 移动多个文件
mv file1.txt file2.txt /backup/

# 移动目录
mv mydir /backup/
```

> **注意**：`mv` 在同一个文件系统内移动文件时只是修改索引，速度极快；跨文件系统移动则相当于先复制后删除。

![[Pasted image 20260628123120.png]]

---

## 3. rm：删除文件或目录

### 基本语法

```bash
rm [选项] 文件...
```

### 常用选项

| 选项 | 全称 | 作用 |
|------|------|------|
| `-r` | recursive | **递归删除**目录及其内容 |
| `-f` | force | **强制删除**，不提示确认 |
| `-i` | interactive | 删除前逐个确认 |
| `-v` | verbose | 显示删除过程 |

### 示例

```bash
# 删除单个文件
rm file.txt

# 删除多个文件
rm file1.txt file2.txt

# 删除目录（必须加 -r）
rm -r mydir/

# 强制删除目录（不提示）
rm -rf mydir/

# 删除前确认（安全模式）
rm -i file.txt
```

> ⚠️ **警告**：`rm -rf /` 会删除根目录下的所有内容，导致系统崩溃。使用该命令前务必确认路径正确！

![[Pasted image 20260628123220.png]]

---

## 4. 通配符删除（模糊删除）

使用 `*` 和 `?` 通配符批量删除：

```bash
# 删除所有 .log 文件
rm *.log

# 删除 file1.txt 到 file9.txt
rm file?.txt

# 删除当前目录下所有文件（保留目录）
rm *

# 删除当前目录下所有内容（包括子目录）
rm -rf *
```

![[Pasted image 20260628123308.png]]

---

## 5. 强制删除

```bash
# 强制删除文件（不提示）
rm -f file.txt

# 强制递归删除目录（最常用，也最危险）
rm -rf mydir/
```

![[Pasted image 20260628123456.png]]

> **安全建议**：
> 1. 删除前先用 `ls` 确认目标文件。
> 2. 重要文件删除前先备份。
> 3. 可以用 `rm -i` 或 `alias rm='rm -i'` 开启安全删除模式。
> 4. 考虑使用 `trash-cli` 等工具将删除操作变为"移入回收站"。

---

## 6. 快速对比

| 操作 | 命令 | 关键选项 |
|------|------|----------|
| 复制文件 | `cp` | `-r` 递归目录，`-a` 保留属性 |
| 移动/重命名 | `mv` | 无需 `-r` 即可移动目录 |
| 删除文件 | `rm` | `-r` 删目录，`-f` 强制，`-rf` 强制递归 |
