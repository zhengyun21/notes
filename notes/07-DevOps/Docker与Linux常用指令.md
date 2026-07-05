
---
## 🐧 Linux 常用指令

### 文件系统操作

| 指令                     | 作用                  |
| ---------------------- | ------------------- |
| `ls`                   | 列出目录内容              |
| `ls -la`               | 显示所有文件（包括隐藏文件）的详细信息 |
| `cd <path>`            | 切换目录                |
| `pwd`                  | 显示当前工作目录路径          |
| `mkdir <dir>`          | 创建新目录               |
| `rm <file>`            | 删除文件                |
| `rm -r <dir>`          | 递归删除目录及内容           |
| `rm -rf <dir>`         | 强制递归删除（慎用）          |
| `cp <src> <dest>`      | 复制文件/目录             |
| `mv <src> <dest>`      | 移动/重命名文件/目录         |
| `touch <file>`         | 创建空文件或更新时间戳         |
| `cat <file>`           | 查看文件内容              |
| `less <file>`          | 分页查看文件内容            |
| `head -n <num> <file>` | 查看文件前 N 行           |
| `tail -n <num> <file>` | 查看文件后 N 行           |
| `tail -f <file>`       | 实时监控文件更新            |

### 系统信息

|指令|作用|
|---|---|
|`uname -a`|显示系统详细信息|
|`lsb_release -a`|显示发行版信息|
|`free -h`|查看内存使用情况|
|`df -h`|查看磁盘空间|
|`top`|实时监控进程|
|`htop`|更友好的进程监控（需安装）|
|`ps aux`|列出所有进程|
|`kill <pid>`|终止指定进程|
|`kill -9 <pid>`|强制终止进程|

### 用户与权限

| 指令                            | 作用         |
| ----------------------------- | ---------- |
| `sudo <cmd>`                  | 以管理员身份执行命令 |
| `chmod <perm> <file>`         | 修改文件权限     |
| `chown <user>:<group> <file>` | 修改文件所有者    |
| `su <user>`                   | 切换用户       |

### 网络操作

|指令|作用|
|---|---|
|`ifconfig` / `ip addr`|查看网络接口信息|
|`ping <host>`|测试网络连通性|
|`curl <url>`|发送 HTTP 请求|
|`wget <url>`|下载文件|
|`netstat -tlnp`|查看监听端口|
|`ss -tlnp`|更高效的端口查看工具|

### 压缩与解压

|指令|作用|
|---|---|
|`tar -czvf <file>.tar.gz <dir>`|压缩为 tar.gz|
|`tar -xzvf <file>.tar.gz`|解压 tar.gz|
|`unzip <file>.zip`|解压 zip 文件|

---

## 🐳 Docker 常用指令

### 镜像操作

|指令|作用|
|---|---|
|`docker pull <image>`|拉取镜像|
|`docker push <image>`|推送镜像到仓库|
|`docker images`|列出本地镜像|
|`docker rmi <image>`|删除镜像|
|`docker build -t <name> <path>`|构建镜像|
|`docker save -o <file>.tar <image>`|导出镜像|
|`docker load -i <file>.tar`|导入镜像|
|`docker tag <src> <dest>`|为镜像打标签|

### 容器操作

|指令|作用|
|---|---|
|`docker run <image>`|创建并运行容器|
|`docker run -d <image>`|后台运行容器|
|`docker run -p <host>:<container> <image>`|端口映射|
|`docker run -v <host>:<container> <image>`|挂载卷|
|`docker run --name <name> <image>`|指定容器名称|
|`docker run -e <key>=<value> <image>`|设置环境变量|
|`docker run --rm <image>`|容器退出后自动删除|
|`docker ps`|列出运行中的容器|
|`docker ps -a`|列出所有容器|
|`docker start <container>`|启动已停止的容器|
|`docker stop <container>`|停止容器|
|`docker restart <container>`|重启容器|
|`docker rm <container>`|删除容器|
|`docker rm -f <container>`|强制删除运行中的容器|
|`docker exec -it <container> <cmd>`|进入容器执行命令|
|`docker logs <container>`|查看容器日志|
|`docker logs -f <container>`|实时查看日志|
|`docker inspect <container>`|查看容器详细信息|

### 网络操作

|指令|作用|
|---|---|
|`docker network ls`|列出网络|
|`docker network create <name>`|创建网络|
|`docker network connect <net> <container>`|连接容器到网络|
|`docker network disconnect <net> <container>`|断开容器网络连接|

### Docker Compose

|指令|作用|
|---|---|
|`docker-compose up`|启动所有服务|
|`docker-compose up -d`|后台启动|
|`docker-compose down`|停止并删除容器|
|`docker-compose ps`|查看服务状态|
|`docker-compose logs`|查看所有服务日志|
|`docker-compose build`|构建服务镜像|
|`docker-compose restart <service>`|重启指定服务|

---

## 💡 常用组合技巧

**1. 查找占用端口的进程：**

lsof -i :<port>  
# 或  
netstat -tlnp | grep <port>

**2. 查看容器资源使用：**

docker stats

**3. 清理无用资源：**

docker system prune  # 清理无用镜像、容器、网络  
docker system prune -a  # 清理所有未使用资源

**4. 批量删除容器：**

docker rm $(docker ps -aq)

**5. 批量删除镜像：**

docker rmi $(docker images -aq)