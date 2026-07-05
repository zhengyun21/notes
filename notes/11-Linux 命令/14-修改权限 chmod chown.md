# chmod 与 chown：修改权限和所有者

> `chmod` 修改文件/目录的权限，`chown` 修改文件/目录的所有者和所属组。

---

## 1. chmod：修改权限（Change Mode）

### 1.1 符号模式（推荐用于精确调整）

```bash
chmod [who][+/-/=][permission] 文件...
```

| 参数 | 含义 |
|------|------|
| `who` | `u`（user/所有者）、`g`（group/组）、`o`（others/其他）、`a`（all/所有） |
| `+` | 增加权限 |
| `-` | 移除权限 |
| `=` | 设置权限（覆盖原有） |
| `permission` | `r`、`w`、`x` 的组合 |

#### 示例

```bash
# 给所有者添加执行权限
chmod u+x script.sh

# 给组用户添加写权限
chmod g+w file.txt

# 移除其他用户的读权限
chmod o-r file.txt

# 给所有人添加执行权限
chmod a+x script.sh
# 等价于
chmod +x script.sh

# 设置所有者读写，组只读，其他无权限
chmod u=rw,g=r,o= file.txt

# 递归修改目录及子文件权限
chmod -R 755 mydir/
```

![[Pasted image 20260630141729.png]]

---

### 1.2 数字模式（推荐用于快速设置）

每个权限位对应一个数字：

| 权限 | 数字 |
|------|------|
| `r`（读） | 4 |
| `w`（写） | 2 |
| `x`（执行） | 1 |
| `-`（无） | 0 |

将需要的权限相加，得到一组三位数字：

```bash
# 权限：rwxr-xr-x
# 所有者：rwx = 4+2+1 = 7
# 组：r-x = 4+0+1 = 5
# 其他：r-x = 4+0+1 = 5
chmod 755 script.sh

# 权限：rw-r--r--
chmod 644 file.txt

# 权限：rwx------
chmod 700 private.key

# 权限：drwxrwxrwx（777，不推荐）
chmod 777 shared/
```

![[Pasted image 20260630141824.png]]

### 1.3 常用数字组合

| 数字 | 权限 | 典型用途 |
|------|------|----------|
| 644 | `rw-r--r--` | 普通文件 |
| 755 | `rwxr-xr-x` | 可执行脚本、目录 |
| 700 | `rwx------` | 私有文件/目录 |
| 600 | `rw-------` | 敏感文件（密钥、密码） |
| 777 | `rwxrwxrwx` | 完全开放（尽量避免） |
| 750 | `rwxr-x---` | 组内共享 |

---

## 2. chown：修改所有者和组（Change Owner）

### 基本语法

```bash
chown [选项] 新所有者[:新组] 文件...
```

### 常用选项

| 选项 | 作用 |
|------|------|
| `-R` | 递归修改目录及所有子文件 |
| `--reference=文件` | 将权限参照另一个文件设置 |

### 示例

```bash
# 只修改文件所有者
sudo chown tomcat file.txt

# 同时修改所有者和所属组
sudo chown tomcat:tomcat file.txt

# 只修改所属组（等价于 chgrp）
sudo chown :developers file.txt

# 递归修改目录及其内容
sudo chown -R tomcat:tomcat /export/server/

# 参照 /etc/passwd 的权限设置给 file.txt
sudo chown --reference=/etc/passwd file.txt
```

![[Pasted image 20260630144340.png]]

---

## 3. chgrp：修改所属组（Change Group）

```bash
# 修改文件所属组
sudo chgrp developers file.txt

# 递归修改
sudo chgrp -R developers /shared/
```

> **注意**：`chown :group` 等价于 `chgrp group`，两者效果相同。

---

## 4. 快速对比

| 需求 | 命令 |
|------|------|
| 给脚本添加执行权限 | `chmod +x script.sh` |
| 设置文件为 755 | `chmod 755 file` |
| 移除某文件的写权限 | `chmod -w file` |
| 递归修改整个目录权限 | `chmod -R 755 mydir/` |
| 修改文件所有者 | `sudo chown user file` |
| 同时修改所有者和组 | `sudo chown user:group file` |
| 只修改所属组 | `sudo chgrp group file` 或 `sudo chown :group file` |
| 查看文件权限 | `ls -l file` |

---

## 5. 常见问题

| 问题 | 原因 | 解决 |
|------|------|------|
| `chmod: changing permissions of 'xxx': Operation not permitted` | 没有该文件的所有权 | 使用 `sudo` 或 `chown` 先获取所有权 |
| 改了权限但还是无法执行 | 可能缺少 `x` 权限，或者文件没有 shebang（`#!/bin/bash`） | 检查 `chmod +x` 和文件头 |
| 目录无法进入 | 缺少 `x` 权限 | `chmod +x dir` |
| 无法删除文件 | 目录没有 `w` 权限 | 检查目录权限，而非文件权限 |
