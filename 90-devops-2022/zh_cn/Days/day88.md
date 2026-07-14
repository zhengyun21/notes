---
title: '#90DaysOfDevOps - 应用聚焦备份 - 第88天'
published: false
description: 90DaysOfDevOps - 应用聚焦备份
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048749
---
## 应用聚焦备份

我们已经在 [第85天](day85.md) 花了一些时间讨论数据服务或数据密集型应用程序，例如数据库。对于这些数据服务，我们必须考虑如何管理一致性，尤其是在应用程序一致性方面。

在这篇文章中，我们将深入探讨以保护应用程序数据一致的方式保护数据的需求。

为此，我们选择的工具将是 [Kanister](https://kanister.io/)

![](Images/Day88_Data1.png)

### Kanister 介绍

Kanister 是 Kasten 的一个开源项目，使我们能够在 Kubernetes 上管理（备份和恢复）应用程序数据。你可以将 Kanister 作为 Helm 应用程序部署到你的 Kubernetes 集群中。

Kanister 使用 Kubernetes 自定义资源，部署 Kanister 时安装的主要自定义资源有

- `Profile`（配置文件）- 是一个用于存储备份和从中恢复的目标位置。最常见的是对象存储。
- `Blueprint`（蓝图）- 备份和恢复数据库应采取的步骤应维护在蓝图中
- `ActionSet`（动作集）- 是将我们的目标备份移动到我们的配置文件以及恢复动作的操作。

### 执行流程演练

在我们动手之前，我们应该看看 Kanister 在保护应用程序数据时采用的工作流程。首先，我们的控制器使用 Helm 部署到我们的 Kubernetes 集群中，Kanister 位于其自己的命名空间中。我们获取蓝图，其中有许多社区支持的蓝图可用，我们稍后将更详细地介绍这一点。然后我们有了我们的数据库工作负载。

![](Images/Day88_Data2.png)

然后我们创建我们的 ActionSet。

![](Images/Day88_Data3.png)

ActionSet 允许我们针对特定的数据服务运行蓝图中定义的动作。

![](Images/Day88_Data4.png)

ActionSet 反过来使用 Kanister 功能（KubeExec、KubeTask、资源生命周期）并将我们的备份推送到我们的目标仓库（Profile）。

![](Images/Day88_Data5.png)

如果该动作完成/失败，相应的状态将在 ActionSet 中更新。

![](Images/Day88_Data6.png)

### 部署 Kanister

我们将再次使用 minikube 集群来实现此应用程序备份。如果你从上一次课程中还保留着它正在运行，那么我们可以继续使用它。

在撰写本文时，我们的镜像版本已更新到 `0.75.0`，使用以下 Helm 命令，我们将把 Kanister 安装到我们的 Kubernetes 集群中。

`helm install kanister --namespace kanister kanister/kanister-operator --set image.tag=0.75.0 --create-namespace`

![](Images/Day88_Data7.png)

我们可以使用 `kubectl get pods -n kanister` 来确保 pod 已启动并运行，然后我们还可以检查我们的自定义资源定义现在是否可用（如果你只安装了 Kanister，那么你会看到高亮的 3 个）

![](Images/Day88_Data8.png)

### 部署数据库

通过 Helm 部署 MySQL：

```
APP_NAME=my-production-app
kubectl create ns ${APP_NAME}
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install mysql-store bitnami/mysql --set primary.persistence.size=1Gi,volumePermissions.enabled=true --namespace=${APP_NAME}
kubectl get pods -n ${APP_NAME} -w
```
![](Images/Day88_Data9.png)

用初始数据填充 MySQL 数据库，运行以下命令：

```
MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace ${APP_NAME} mysql-store -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
MYSQL_HOST=mysql-store.${APP_NAME}.svc.cluster.local
MYSQL_EXEC="mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD} -DmyImportantData -t"
echo MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
```

### 创建 MySQL 客户端
我们将运行另一个容器镜像作为我们的客户端

```
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```
```
注意：如果你已经有一个正在运行的 mysql 客户端 pod，请使用以下命令删除

kubectl delete pod -n ${APP_NAME} mysql-client
```

### 向 MySQL 添加数据

```
echo "create database myImportantData;" | mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD}
MYSQL_EXEC="mysql -h ${MYSQL_HOST} -u root --password=${MYSQL_ROOT_PASSWORD} -DmyImportantData -t"
echo "drop table Accounts" | ${MYSQL_EXEC}
echo "create table if not exists Accounts(name text, balance integer); insert into Accounts values('nick', 0);" |  ${MYSQL_EXEC}
echo "insert into Accounts values('albert', 112);" | ${MYSQL_EXEC}
echo "insert into Accounts values('alfred', 358);" | ${MYSQL_EXEC}
echo "insert into Accounts values('beatrice', 1321);" | ${MYSQL_EXEC}
echo "insert into Accounts values('bartholomew', 34);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edward', 5589);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edwin', 144);" | ${MYSQL_EXEC}
echo "insert into Accounts values('edwina', 233);" | ${MYSQL_EXEC}
echo "insert into Accounts values('rastapopoulos', 377);" | ${MYSQL_EXEC}
echo "select * from Accounts;" |  ${MYSQL_EXEC}
exit
```
你应该能够看到如下所示的一些数据。

![](Images/Day88_Data10.png)


### 创建 Kanister 配置文件

Kanister 提供了一个 CLI `kanctl` 和另一个实用程序 `kando`，用于从蓝图与对象存储提供商交互，这两个实用程序都会用到。

[CLI 下载](https://docs.kanister.io/tooling.html#tooling)

我已经创建了一个 AWS S3 存储桶，我们将用作配置文件目标和恢复位置。我将使用环境变量，这样我仍然能够向你展示我使用 `kanctl` 创建 Kanister 配置文件的命令。

`kanctl create profile s3compliant --access-key $ACCESS_KEY --secret-key $SECRET_KEY --bucket $BUCKET --region eu-west-2 --namespace my-production-app`

![](Images/Day88_Data11.png)

### 蓝图时间

别担心，你不需要从头开始创建自己的蓝图，除非你的数据服务没有列在 [Kanister 示例](https://github.com/kanisterio/kanister/tree/master/examples) 中，但无论如何，社区贡献是这个项目获得认知度的方式。

我们将使用的蓝图如下。


```
apiVersion: cr.kanister.io/v1alpha1
kind: Blueprint
metadata:
  name: mysql-blueprint
actions:
  backup:
    outputArtifacts:
      mysqlCloudDump:
        keyValue:
          s3path: "{{ .Phases.dumpToObjectStore.Output.s3path }}"
    phases:
    - func: KubeTask
      name: dumpToObjectStore
      objects:
        mysqlSecret:
          kind: Secret
          name: '{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}'
          namespace: '{{ .StatefulSet.Namespace }}'
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .StatefulSet.Namespace }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="/mysql-backups/{{ .StatefulSet.Namespace }}/{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}/{{ toDate "2006-01-02T15:04:05.999999999Z07:00" .Time  | date "2006-01-02T15-04-05" }}/dump.sql.gz"
          root_password="{{ index .Phases.dumpToObjectStore.Secrets.mysqlSecret.Data "mysql-root-password" | toString }}"
          mysqldump --column-statistics=0 -u root --password=${root_password} -h {{ index .Object.metadata.labels "app.kubernetes.io/instance" }} --single-transaction --all-databases | gzip - | kando location push --profile '{{ toJson .Profile }}' --path ${s3_path} -
          kando output s3path ${s3_path}
  restore:
    inputArtifactNames:
    - mysqlCloudDump
    phases:
    - func: KubeTask
      name: restoreFromBlobStore
      objects:
        mysqlSecret:
          kind: Secret
          name: '{{ index .Object.metadata.labels "app.kubernetes.io/instance" }}'
          namespace: '{{ .StatefulSet.Namespace }}'
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .StatefulSet.Namespace }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="{{ .ArtifactsIn.mysqlCloudDump.KeyValue.s3path }}"
          root_password="{{ index .Phases.restoreFromBlobStore.Secrets.mysqlSecret.Data "mysql-root-password" | toString }}"
          kando location pull --profile '{{ toJson .Profile }}' --path ${s3_path} - | gunzip | mysql -u root --password=${root_password} -h {{ index .Object.metadata.labels "app.kubernetes.io/instance" }}
  delete:
    inputArtifactNames:
    - mysqlCloudDump
    phases:
    - func: KubeTask
      name: deleteFromBlobStore
      args:
        image: ghcr.io/kanisterio/mysql-sidecar:0.75.0
        namespace: "{{ .Namespace.Name }}"
        command:
        - bash
        - -o
        - errexit
        - -o
        - pipefail
        - -c
        - |
          s3_path="{{ .ArtifactsIn.mysqlCloudDump.KeyValue.s3path }}"
          kando location delete --profile '{{ toJson .Profile }}' --path ${s3_path}
```

要添加这个，我们将使用 `kubectl create -f mysql-blueprint.yml -n kanister` 命令

![](Images/Day88_Data12.png)

### 创建我们的 ActionSet 并保护我们的应用程序

现在我们将使用定义了此应用程序备份的 ActionSet 来备份 MySQL 数据。在与控制器相同的命名空间中创建 ActionSet。

`kubectl get profiles.cr.kanister.io -n my-production-app` 此命令将显示我们之前创建的配置文件，我们可以在这里配置多个配置文件，因此我们可能希望为不同的 ActionSet 使用特定的配置文件

然后我们将使用 `kanctl` 创建我们的 ActionSet

`kanctl create actionset --action backup --namespace kanister --blueprint mysql-blueprint --statefulset my-production-app/mysql-store --profile my-production-app/s3-profile-dc5zm --secrets mysql=my-production-app/mysql-store`

从上面的命令中你可以看到，我们定义了添加到命名空间中的蓝图、`my-production-app` 命名空间中的 statefulset，以及进入 MySQL 应用程序的 secrets。

![](Images/Day88_Data13.png)

通过获取 ActionSet 名称并使用此命令 `kubectl --namespace kanister describe actionset backup-qpnqv` 来检查 ActionSet 的状态。

最后我们可以去确认我们的 AWS S3 存储桶中现在有数据。

![](Images/Day88_Data14.png)

### 恢复

在恢复任何东西之前，我们需要制造一些破坏，我们可以通过删除表来实现，也许是意外，也许不是。

连接到我们的 MySQL pod。

```
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```

你可以通过 `echo "SHOW DATABASES;" |  ${MYSQL_EXEC}` 看到我们的 importantdata 数据库在那里

然后要删除，我们运行 `echo "DROP DATABASE myImportantData;" |  ${MYSQL_EXEC}`

并通过几次尝试确认它已经消失。

![](Images/Day88_Data15.png)

现在我们可以使用 Kanister 来恢复我们的重要数据，使用 `kubectl get actionset -n kanister` 来查找我们之前拍摄的 ActionSet 名称。然后我们将创建一个恢复 ActionSet 来恢复我们的数据，使用 `kanctl create actionset -n kanister --action restore --from "backup-qpnqv"`

![](Images/Day88_Data16.png)

我们可以通过使用以下命令连接到我们的数据库来确认我们的数据已恢复。

```
APP_NAME=my-production-app
kubectl run mysql-client --rm --env APP_NS=${APP_NAME} --env MYSQL_EXEC="${MYSQL_EXEC}" --env MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} --env MYSQL_HOST=${MYSQL_HOST} --namespace ${APP_NAME} --tty -i --restart='Never' --image  docker.io/bitnami/mysql:latest --command -- bash
```
现在我们在 MySQL 客户端内部，我们可以执行 `echo "SHOW DATABASES;" |  ${MYSQL_EXEC}`，我们可以看到数据库回来了。我们还可以执行 `echo "select * from Accounts;" |  ${MYSQL_EXEC}` 来检查数据库的内容，我们的重要数据已经恢复。

![](Images/Day88_Data17.png)

在下一篇文章中，我们将看看 Kubernetes 中的灾难恢复。

## 相关资料

- [Kanister Overview - An extensible open-source framework for app-lvl data management on Kubernetes](https://www.youtube.com/watch?v=wFD42Zpbfts)
- [Application Level Data Operations on Kubernetes](https://community.cncf.io/events/details/cncf-cncf-online-programs-presents-cncf-live-webinar-kanister-application-level-data-operations-on-kubernetes/)
- [Kubernetes Backup and Restore made easy!](https://www.youtube.com/watch?v=01qcYSck1c4&t=217s)
- [Kubernetes Backups, Upgrades, Migrations - with Velero](https://www.youtube.com/watch?v=zybLTQER0yY)
- [7 Database Paradigms](https://www.youtube.com/watch?v=W2Z7fbCLSTw&t=520s)
- [Disaster Recovery vs. Backup: What's the difference?](https://www.youtube.com/watch?v=07EHsPuKXc0)
- [Veeam Portability & Cloud Mobility](https://www.youtube.com/watch?v=hDBlTdzE6Us&t=3s)

[第89天](day89.md)见。
