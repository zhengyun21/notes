# 基于 minikube 进行 k8s 实操

## 启动集群
    minikube start --driver=docker

## 获取节点
    kubectl get nodes

## 打开 web ui
    minikube dashboard

## 部署资源
    kubectl apply -f xxx.yaml

## 删除资源
    kubectl delete -f xxx.yaml

## 实战
    1. 创建 deployment.yaml
    2. 创建 service.yaml
    3. 部署

## 获取访问地址
    minikube service [serviceName] --url
    端口转发：kubectl port-forward svc/web-service 8080:80

## 进入容器
    kubectl exec -it [pod.Id] -- /bash || /sh

## 报错

### 1. 出现 Exiting due to DRV_AS_ROOT: The “docker“ driver should not be used with root privileges
    minikube start -f --driver=docker

### 2. deployment.xxx unchanged
    - 网络超时，切换国内镜像，在 yaml 文件里的 image 后换成国内镜像地址。
    - 或者先手动下载镜像。

### 3. OCI runtime exec failed: exec failed: unable to start container process: exec: "/bash": stat /bash: no such file or directory command terminated with exit code 127
    镜像没有 /bash 文件，可以使用 sh 进入容器：
    kubectl exec -it [pod.Id] -- sh