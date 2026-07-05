# yum：CentOS/RHEL 软件包管理

> `yum`（Yellowdog Updater, Modified）是 CentOS、RHEL、Fedora 等基于 RPM 的发行版中用于软件包管理的命令行工具。

---

## 基本语法

```bash
yum [选项] [命令] [包名...]
```

---

## 常用命令

| 命令 | 作用 |
|------|------|
| `yum install 包名` | 安装软件包 |
| `yum remove 包名` | 卸载软件包 |
| `yum update` | 更新所有已安装的软件包 |
| `yum update 包名` | 更新指定软件包 |
| `yum search 关键词` | 搜索软件包 |
| `yum list` | 列出所有可用和已安装的包 |
| `yum list installed` | 列出已安装的软件包 |
| `yum info 包名` | 查看软件包详细信息 |
| `yum clean all` | 清理缓存 |
| `yum repolist` | 列出已配置的仓库 |

---

## 常用选项

| 选项 | 作用 |
|------|------|
| `-y` | 自动回答 yes（无需确认） |
| `--nogpgcheck` | 跳过 GPG 签名检查 |
| `--enablerepo=仓库名` | 临时启用某个仓库 |
| `--disablerepo=仓库名` | 临时禁用某个仓库 |

---

## 示例

```bash
# 安装 nginx（自动确认）
sudo yum install -y nginx

# 卸载软件
sudo yum remove -y nginx

# 搜索包含 "mysql" 的软件包
yum search mysql

# 查看已安装的包
yum list installed | grep nginx

# 更新所有软件
sudo yum update -y

# 查看软件包信息
yum info nginx

# 清理缓存
sudo yum clean all
```

![[Pasted image 20260630153427.png]]

![[Pasted image 20260630153437.png]]

---

## 仓库配置

yum 的仓库配置文件存放在 `/etc/yum.repos.d/` 目录下，以 `.repo` 结尾：

```bash
# 查看仓库配置
ls /etc/yum.repos.d/

# 查看某个仓库文件内容
cat /etc/yum.repos.d/CentOS-Base.repo
```

---

## 快速对比

| 需求 | 命令 |
|------|------|
| 安装软件 | `sudo yum install -y 包名` |
| 卸载软件 | `sudo yum remove -y 包名` |
| 搜索软件 | `yum search 关键词` |
| 更新系统 | `sudo yum update -y` |
| 查看已安装 | `yum list installed` |
| 清理缓存 | `sudo yum clean all` |

> **注意**：CentOS 8 及更新版本推荐使用 `dnf` 代替 `yum`，命令和用法几乎完全一致。
