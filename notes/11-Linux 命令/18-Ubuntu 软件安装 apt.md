# apt：Debian/Ubuntu 软件包管理

> `apt`（Advanced Package Tool）是 Debian、Ubuntu 及其衍生发行版的软件包管理工具，功能与 `yum` 类似，但命令和用法有所不同。

---

## 基本语法

```bash
apt [选项] 命令 [包名...]
```

> 注意：在 Ubuntu 16.04 之前推荐使用 `apt-get`，之后推荐使用更简洁的 `apt` 命令。

---

## 常用命令

| 命令 | 作用 |
|------|------|
| `apt update` | **更新软件包列表**（从仓库获取最新信息） |
| `apt upgrade` | **升级所有已安装的软件包** |
| `apt install 包名` | 安装软件包 |
| `apt remove 包名` | 卸载软件包（保留配置文件） |
| `apt purge 包名` | 彻底卸载（删除配置文件） |
| `apt autoremove` | 自动删除不再需要的依赖包 |
| `apt search 关键词` | 搜索软件包 |
| `apt show 包名` | 显示软件包详细信息 |
| `apt list` | 列出所有可用包 |
| `apt list --installed` | 列出已安装的包 |
| `apt full-upgrade` | 完整升级（可能删除旧包，解决依赖） |

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-y` | 自动确认所有提示 |
| `--no-install-recommends` | 不安装推荐的依赖（最小化安装） |

---

## 示例

```bash
# 更新软件包列表（每次安装前建议先执行）
sudo apt update

# 升级所有软件
sudo apt upgrade -y

# 安装 nginx
sudo apt install -y nginx

# 卸载 nginx
sudo apt remove -y nginx

# 彻底卸载（包括配置文件）
sudo apt purge -y nginx

# 搜索包含 "mysql" 的包
apt search mysql

# 查看 nginx 包信息
apt show nginx

# 清理不再需要的依赖
sudo apt autoremove -y
```

![[Pasted image 20260630153622.png]]

---

## 完整更新流程

```bash
# 1. 更新包列表
sudo apt update

# 2. 升级所有已安装的包
sudo apt upgrade -y

# 3. 清理不再需要的依赖
sudo apt autoremove -y

# 4. 清理已下载的包缓存
sudo apt clean
```

---

## 快速对比

| 需求 | apt 命令 | yum 命令 |
|------|----------|----------|
| 更新包列表 | `sudo apt update` | `sudo yum makecache` |
| 升级软件 | `sudo apt upgrade -y` | `sudo yum update -y` |
| 安装软件 | `sudo apt install -y 包名` | `sudo yum install -y 包名` |
| 卸载软件 | `sudo apt remove -y 包名` | `sudo yum remove -y 包名` |
| 彻底卸载 | `sudo apt purge -y 包名` | — |
| 搜索软件 | `apt search 关键词` | `yum search 关键词` |
| 清理依赖 | `sudo apt autoremove` | `sudo yum autoremove` |

> **注意**：`apt update` 只是更新软件列表，不会安装或升级任何软件。必须先 `update` 再 `upgrade`！
