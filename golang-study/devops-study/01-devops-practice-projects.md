# GitHub 运维练手项目清单

整理日期：2026-07-02

这份清单适合用来练习 DevOps / 运维相关技能，包括 Docker、Docker Compose、Kubernetes、Ansible、Terraform、CI/CD、监控和云上部署。建议先从本地可跑的项目开始，再逐步过渡到 Kubernetes 和云资源项目。

## 推荐项目

| 项目 | 适合阶段 | 主要练习内容 | 备注 |
| --- | --- | --- | --- |
| [docker/awesome-compose](https://github.com/docker/awesome-compose) | 入门 | Docker Compose、多服务编排、数据库、中间件、本地部署 | 官方样例合集，适合先练手 |
| [kubernetes/examples](https://github.com/kubernetes/examples) | 入门到进阶 | Kubernetes Deployment、Service、Pod、应用部署 | Kubernetes 官方示例仓库 |
| [ansible/ansible-examples](https://github.com/ansible/ansible-examples) | 入门 | Ansible playbook、角色、批量配置 | 仓库已归档，适合学习基础写法 |
| [MichaelCade/90DaysOfDevOps](https://github.com/MichaelCade/90DaysOfDevOps) | 系统学习 | DevOps 路线、Linux、网络、容器、云、CI/CD | 适合按天打卡学习 |
| [bregman-arie/devops-exercises](https://github.com/bregman-arie/devops-exercises) | 系统练习 | Linux、Git、Docker、Kubernetes、Terraform、Ansible、Prometheus、CI/CD | 题库型仓库，适合查漏补缺 |
| [techiescamp/devops-projects](https://github.com/techiescamp/devops-projects) | 进阶 | Jenkins、Prometheus、Terraform、AWS、GitHub Actions、服务发现 | 更接近真实项目，云资源可能产生费用 |
| [wardviaene/terraform-course](https://github.com/wardviaene/terraform-course) | 进阶 | Terraform、AWS、基础设施即代码、流水线 | 适合专项练 Terraform |

## 建议练习路线

### 1. Docker / Compose 入门

先从 [docker/awesome-compose](https://github.com/docker/awesome-compose) 选择几个简单项目运行：

- Web 应用 + 数据库
- Redis / PostgreSQL / MySQL 组合
- Nginx 反向代理
- ELK 或监控类服务

重点练习：

- 阅读 `Dockerfile`
- 阅读 `docker-compose.yml`
- 使用 `docker compose up -d` 启动服务
- 查看容器日志
- 理解端口映射、卷挂载、环境变量

### 2. Kubernetes 基础

然后练 [kubernetes/examples](https://github.com/kubernetes/examples)：

- 部署一个 Web 应用
- 创建 Service 暴露服务
- 修改副本数
- 查看 Pod 状态和日志
- 尝试滚动更新和回滚

重点练习：

- `kubectl apply`
- `kubectl get`
- `kubectl describe`
- `kubectl logs`
- Deployment、Service、ConfigMap、Secret 的基本用法

### 3. Ansible 自动化配置

使用 [ansible/ansible-examples](https://github.com/ansible/ansible-examples) 练习基础 playbook：

- 安装软件包
- 修改配置文件
- 启动和重启服务
- 使用变量和模板
- 分组管理主机

注意：这个仓库已经归档，适合学习基础语法，不建议完全照搬为生产实践。

### 4. 系统刷题和查漏补缺

用 [bregman-arie/devops-exercises](https://github.com/bregman-arie/devops-exercises) 检查自己哪些知识点不熟：

- Linux 命令
- 网络基础
- Git
- Docker
- Kubernetes
- Terraform
- Ansible
- Prometheus
- CI/CD

建议做法：遇到不会的问题，不只看答案，要自己在本机或虚拟机里实际执行一遍。

### 5. 真实项目实战

最后再做 [techiescamp/devops-projects](https://github.com/techiescamp/devops-projects) 或 [wardviaene/terraform-course](https://github.com/wardviaene/terraform-course)：

- 用 Terraform 创建云资源
- 配置 Jenkins 或 GitHub Actions 流水线
- 部署服务到云环境
- 接入 Prometheus / Grafana 监控
- 配置服务发现或高可用组件

注意：涉及 AWS、云服务器、负载均衡、数据库等资源时，可能产生费用。练完后要及时销毁资源。

## 结合 Go 的练手项目建议

如果想把当前 Go 学习仓库和运维练习结合起来，可以做一个小型完整链路：

1. 写一个 Go HTTP 服务。
2. 添加健康检查接口，例如 `/healthz`。
3. 编写 `Dockerfile`。
4. 编写 `docker-compose.yml`，本地启动 Go 服务。
5. 编写 Kubernetes YAML，包括 Deployment 和 Service。
6. 添加 GitHub Actions，实现提交代码后自动测试和构建镜像。
7. 可选：接入 Prometheus 指标接口和 Grafana 看板。

这个小项目能同时练到 Go、容器化、编排、CI/CD 和基础可观测性，比单独看教程更容易形成完整理解。

## 推荐顺序

1. `docker/awesome-compose`
2. `kubernetes/examples`
3. `ansible/ansible-examples`
4. `bregman-arie/devops-exercises`
5. `techiescamp/devops-projects`
6. `wardviaene/terraform-course`

