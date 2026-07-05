# IP、主机名与固定 IP 配置

> 网络配置是 Linux 服务器管理的基础，包括查看 IP 地址、设置主机名、配置固定 IP 等。

---

## 1. 查看 IP 地址

### ifconfig（传统命令）

```bash
# 查看所有网卡信息
ifconfig

# 查看指定网卡
ifconfig eth0

# 查看简化信息
ifconfig -a
```

![[Pasted image 20260702143635.png]]

### ip（现代推荐命令）

```bash
# 查看所有 IP 地址
ip addr
# 简写
ip a

# 查看指定网卡
ip addr show eth0

# 查看路由表
ip route

# 查看网络统计
ip -s link
```

> **注意**：新版 Linux 推荐使用 `ip` 命令代替 `ifconfig`。`ifconfig` 属于 net-tools 包，部分最小化系统未预装。

---

## 2. 配置主机名（Hostname）

### 临时修改（重启失效）

```bash
sudo hostname 新主机名
```

### 永久修改

```bash
# 方法1：使用 hostnamectl（推荐）
sudo hostnamectl set-hostname 新主机名

# 方法2：直接修改配置文件
sudo vim /etc/hostname

# 同时修改 /etc/hosts，确保本地解析正常
sudo vim /etc/hosts
# 添加：127.0.0.1  新主机名
```

### 查看当前主机名

```bash
hostname
hostnamectl
```

![[Pasted image 20260702143751.png]]

---

## 3. 配置固定 IP（静态 IP）

### VMware 虚拟机中配置固定 IP

以 CentOS 7 为例，编辑网卡配置文件：

```bash
# 1. 查看网卡名称
ip addr

# 2. 编辑网卡配置文件
sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33

# 3. 修改以下内容
TYPE=Ethernet
BOOTPROTO=static        # 将 dhcp 改为 static
ONBOOT=yes              # 开机启动
IPADDR=192.168.88.130   # 固定 IP
NETMASK=255.255.255.0   # 子网掩码
GATEWAY=192.168.88.2    # 网关
DNS1=8.8.8.8            # DNS
DNS2=114.114.114.114
```

### Ubuntu 配置固定 IP

```bash
# 编辑 netplan 配置文件
sudo vim /etc/netplan/00-installer-config.yaml

# 内容示例：
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 114.114.114.114

# 应用配置
sudo netplan apply
```

### 重启网络服务

```bash
# CentOS/RHEL
sudo systemctl restart network

# 或仅重启指定网卡
sudo ifdown eth0 && sudo ifup eth0

# Ubuntu
sudo netplan apply
```

---

## 4. 快速对比

| 需求 | 命令/方法 |
|------|-----------|
| 查看 IP | `ip addr` 或 `ifconfig` |
| 查看公网 IP | `curl ifconfig.me` |
| 查看主机名 | `hostname` |
| 修改主机名 | `sudo hostnamectl set-hostname 新名字` |
| 配置固定 IP | 编辑网卡配置文件 |
| 测试网络连通 | `ping 8.8.8.8` |
| 查看 DNS | `cat /etc/resolv.conf` |
| 查看路由 | `ip route` |

> **VMware 网络模式**：NAT 模式（最常用，虚拟机共享主机 IP 上网）、桥接模式（虚拟机独立 IP）、仅主机模式（仅主机与虚拟机通信）。配置固定 IP 时，IP 地址要与 VMware 虚拟网卡的网段一致。
