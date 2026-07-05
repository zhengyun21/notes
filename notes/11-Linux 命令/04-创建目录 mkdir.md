# mkdir：创建目录

> `mkdir`（Make Directory）用于在 Linux 中创建新的文件夹（目录）。

---

## 基本语法

```bash
mkdir [选项] 目录名
```

---

## 常用选项

| 选项 | 全称 | 作用 |
|------|------|------|
| `-p` | parents | **递归创建**多级目录（如果父目录不存在则自动创建） |
| `-v` | verbose | 显示创建过程信息 |

---

## 示例

```bash
# 创建单个目录
mkdir myfolder

# 创建多级目录（一次性创建 a/b/c）
mkdir -p a/b/c

# 创建多个同级目录
mkdir dir1 dir2 dir3

# 创建时显示详细信息
mkdir -v newdir

# 组合使用：递归创建并显示信息
mkdir -pv projects/2024/linux
```

![[Pasted image 20260627155626.png]]

---

## 常见错误

| 错误提示 | 原因 | 解决 |
|----------|------|------|
| `Permission denied` | 没有当前目录的写入权限 | 使用 `sudo` 或切换到合适目录 |
| `File exists` | 目录已存在 | 检查目录名，或先删除/重命名 |
| `No such file or directory` | 父目录不存在 | 使用 `-p` 选项递归创建 |

---

## 快速参考

| 需求 | 命令 |
|------|------|
| 创建一个目录 | `mkdir dirname` |
| 创建多级目录 | `mkdir -p a/b/c` |
| 创建多个目录 | `mkdir dir1 dir2 dir3` |
