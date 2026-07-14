---
title: '#90DaysOfDevOps - Tags（标签）、Variables（变量）、Inventory（清单）和数据库服务器配置 - 第68天'
published: false
description: '90DaysOfDevOps - Tags（标签）、Variables（变量）、Inventory（清单）和数据库服务器配置'
tags: "devops, 90daysofdevops, learning"
cover_image: null
canonical_url: null
id: 1048780
---
## Tags（标签）、Variables（变量）、Inventory（清单）和数据库服务器配置

### Tags（标签）

按照昨天 session 结束时的 playbook 状态，我们需要运行 playbook 中的每一个 task 和 play。这意味着我们必须让 Web 服务器和负载均衡器的 plays 和 tasks 全部运行完毕。

然而，tags（标签）使我们能够在需要时将它们分开。如果我们的环境中有超大且冗长的 playbook，这可能是一个高效的举措。

在我们的 playbook 文件中，这里我们使用的是 [ansible-scenario5](Configmgmt/ansible-scenario5/playbook5.yml)

```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 66!"
  roles:
    - common
    - apache2
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```
我们可以使用 `ansible-playbook playbook5.yml --list-tags` 来确认这一点，list tags 将列出我们在 playbook 中定义的标签。

![](Images/Day68_config1.png)

现在，如果我们只想针对 proxy（代理）运行，我们可以通过运行 `ansible-playbook playbook5.yml --tags proxy` 来实现，如下所示，这将只对 proxy 运行 playbook。

![](Images/Day68_config2.png)

tags 也可以添加到 task 级别，这样我们就可以非常精细地控制在哪里以及执行什么操作。可以是应用程序相关的 tags，例如我们可以遍历 tasks 并根据安装、配置或卸载来标记我们的 tasks。另一个非常有用的 tag 是：

`tag: always` 这将确保无论你在命令中使用什么 `--tags`，如果某个任务被标记了 always 值，那么当你运行 ansible-playbook 命令时，它总会被执行。

使用 tags，我们还可以将多个 tags 捆绑在一起，如果我们选择运行 `ansible-playbook playbook5.yml --tags proxy,web`，这将运行所有带有这些 tags 的项目。显然，在我们的实例中，这意味着和运行整个 playbook 一样，但如果我们有多个其他 plays，那么这就有意义了。

你也可以定义多个 tag。

### Variables（变量）

Ansible 中有两种主要类型的变量。

- 用户创建的（User created）
- Ansible Facts（Ansible 事实）

### Ansible Facts

每次我们运行 playbook 时，都会有一个我们没有定义的任务叫做 "Gathering facts"（收集事实），我们可以使用这些变量或 facts（事实）来让我们的自动化任务执行某些操作。

![](Images/Day68_config3.png)

如果我们运行以下命令 `ansible proxy -m setup`，我们应该会看到大量的 JSON 格式输出。不过终端上会有很多信息，要真正使用这些信息，我们希望将它输出到一个文件，使用 `ansible proxy -m setup >> facts.json`，你可以在这个仓库中看到此文件，[ansible-scenario5](Configmgmt/ansible-scenario5/facts.json)

![](Images/Day68_config4.png)

如果你打开这个文件，你可以看到各种关于我们命令的信息。我们可以获取 IP 地址、架构、bios 版本等。如果我们想利用这些信息并在我们的 playbook 中使用，这些信息非常有用。

一个想法是，在我们硬编码了 Web 服务器 IP 地址的 nginx 模板 mysite.j2 中使用这些变量之一。你可以在 mysite.j2 中创建一个 for 循环，这将遍历 [webservers] 组，这使我们能够自动动态地创建或添加超过 2 个 Web 服务器到这个负载均衡器配置中。

```
#Dynamic Config for server {{ ansible_facts['nodename'] }}
    upstream webservers {
	{% for host in groups['webservers'] %}
        server {{ hostvars[host]['ansible_facts']['nodename'] }}:8000;
    {% endfor %}
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```
上面的结果现在看起来和之前一样，但如果我们添加更多 Web 服务器或删除一个，这将动态更改代理配置。为此，你需要配置好名称解析。

### User created（用户创建的）

用户创建的变量就是我们自己创建的变量。如果你查看我们的 playbook，你会看到我们有 `vars:`，然后列出了 3 个我们正在使用的变量。

```
- hosts: webservers
  become: yes
  vars:
    http_port: 8000
    https_port: 4443
    html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"
  roles:
    - common
    - apache2
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```

然而，我们可以将变量从 playbook 中移出，放到它们自己的文件中，从而保持 playbook 的清晰。我们将这样做，但我们会移动到 [ansible-scenario6](Configmgmt/ansible-scenario6) 文件夹。在该文件夹的根目录中，我们将创建一个 group_vars 文件夹。然后我们将创建另一个名为 all 的文件夹（所有组都将获取这些变量）。在那里，我们将创建一个名为 `common_variables.yml` 的文件，并将我们的变量从 playbook 复制到这个文件中。同时从 playbook 中移除它们以及 vars: 这一行。

```
http_port: 8000
https_port: 4443
html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"
```

因为我们将其作为全局变量，所以我们还可以在这里添加 NTP 和 DNS 服务器。这些变量是通过我们创建的文件夹结构来设置的。你可以在下文看到我们的 Playbook 现在看起来多么简洁。

```
- hosts: webservers
  become: yes
  roles:
    - common
    - apache2
  tags: web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags: proxy
```

其中一个变量是 http_port，我们可以在 mysite.j2 中的 for 循环中再次使用它，如下所示：

```
#Dynamic Config for server {{ ansible_facts['nodename'] }}
    upstream webservers {
	{% for host in groups['webservers'] %}
        server {{ hostvars[host]['ansible_facts']['nodename'] }}:{{ http_port }};
    {% endfor %}
    }

    server {
        listen 80;

        location / {
                proxy_pass http://webservers;
        }
    }
```

我们还可以在 roles/apache2/templates/index.html.j2 文件中定义一个 ansible fact，这样我们就能知道我们当前在哪个 Web 服务器上。

```
<html>

<h1>{{ html_welcome_msg }}! I'm webserver {{ ansible_facts['nodename'] }} </h1>

</html>
```
运行带有变量更改的 `ansible-playbook playbook6.yml` 命令的结果意味着，当我们访问负载均衡器时，你可以看到我们命中了组中的任意一台 Web 服务器。

![](Images/Day68_config5.png)

如果我们愿意，我们还可以添加一个名为 host_vars 的文件夹，并创建一个 web01.yml，为每台主机设置特定的消息或更改内容。

### Inventory Files（清单文件）

到目前为止，我们一直使用 /etc/ansible 文件夹中的默认 hosts 文件来确定我们的主机。然而，我们可以为不同的环境创建不同的文件，例如生产和预发布环境。我不打算创建更多环境。但我们可以创建自己的主机文件。

我们可以为不同的服务器和节点清单创建多个文件。我们将使用 `ansible-playbook -i dev playbook.yml` 来调用这些文件。你也可以在 hosts 文件中定义变量，然后在 playbook 的其他地方打印或使用该变量，例如在我正在跟进的示例和培训课程中，他们在 hosts 文件中添加了 environment（环境）变量，并将其添加到负载均衡器网页模板中，以在网页消息中显示环境信息。

### 部署我们的数据库服务器

我们还有一台尚未启动和配置的机器。我们可以使用 `vagrant up db01` 从我们的 Vagrantfile 所在位置启动它。当它启动并可访问后，我们需要确保使用 `ssh-copy-id db01` 复制 SSH 密钥，以便我们可以访问它。

我们将从 [ansible-scenario7](Configmgmt/ansible-scenario7) 文件夹开始工作。

然后让我们使用 `ansible-galaxy init roles/mysql` 为一个名为 "mysql" 的新 role 创建一个新的文件夹结构。

在我们的 playbook 中，我们将添加一个新的 play 块用于数据库配置。我们在 /etc/ansible/hosts 文件中定义了 database 组。然后我们指示 database 组拥有 common role 和一个上一步创建的新 role mysql。我们还用 database 标记了我们的 database 组，这意味着正如我们之前讨论的，如果我们愿意，我们可以选择只针对这些 tags 运行。

```
- hosts: webservers
  become: yes
  roles:
    - common
    - apache2
  tags:
    web

- hosts: proxy
  become: yes
  roles:
    - common
    - nginx
  tags:
    proxy

- hosts: database
  become: yes
  roles:
    - common
    - mysql
  tags: database
```

在我们的 roles 文件夹结构中，tree 命令会自动创建，我们需要填充以下内容：

Handlers - main.yml

```
# handlers file for roles/mysql
- name: restart mysql
  service:
    name: mysql
    state: restarted
```

Tasks - install_mysql.yml, main.yml & setup_mysql.yml

install_mysql.yml - 这个 task 将用于安装 mysql 并确保服务正在运行。

```
- name: "Install Common packages"
  apt: name={{ item }} state=latest
  with_items:
   - python3-pip
   - mysql-client
   - python3-mysqldb
   - libmysqlclient-dev

- name: Ensure mysql-server is installed latest version
  apt: name=mysql-server state=latest

- name: Installing python module MySQL-python
  pip:
    name: PyMySQL

- name: Ensure mysql-server is running
  service:
    name: mysql
    state: started
```

main.yml 是一个指针文件，指示我们从这些文件中 import_tasks（导入任务）。

```
# tasks file for roles/mysql
- import_tasks: install_mysql.yml
- import_tasks: setup_mysql.yml
```

setup_mysql.yml - 这个 task 将创建我们的数据库和数据库用户。

```
- name: Create my.cnf configuration file
  template: src=templates/my.cnf.j2 dest=/etc/mysql/conf.d/mysql.cnf
  notify: restart mysql

- name: Create database user with name 'devops' and password 'DevOps90' with all database privileges
  community.mysql.mysql_user:
    login_unix_socket: /var/run/mysqld/mysqld.sock
    login_user: "{{ mysql_user_name }}"
    login_password: "{{ mysql_user_password }}"
    name: "{{db_user}}"
    password: "{{db_pass}}"
    priv: '*.*:ALL'
    host: '%'
    state: present

- name: Create a new database with name '90daysofdevops'
  mysql_db:
    login_user: "{{ mysql_user_name }}"
    login_password: "{{ mysql_user_password }}"
    name: "{{ db_name }}"
    state: present
```

你可以从上面看到，我们使用一些变量来确定我们的一些配置，例如密码、用户名和数据库，这些都存储在我们的 group_vars/all/common_variables.yml 文件中。

```
http_port: 8000
https_port: 4443
html_welcome_msg: "Hello 90DaysOfDevOps - Welcome to Day 68!"

mysql_user_name: root
mysql_user_password: "vagrant"
db_user: devops
db_pass: DevOps90
db_name: 90DaysOfDevOps
```
我们在 templates 文件夹中还有 my.cnf.j2 文件，看起来如下：

```
[mysql]
bind-address = 0.0.0.0
```

### 运行 playbook

现在我们的虚拟机已经启动并运行，我们的配置文件也已就位，我们现在准备运行我们的 playbook，如果我们运行 `ansible-playbook playbook7.yml`，它将包含我们之前做的所有内容，或者我们可以选择只部署到我们的 database 组，使用 `ansible-playbook playbook7.yml --tags database` 命令，这将只运行我们的新配置文件。

我只针对 database tag 运行，但我遇到了一个错误。这个错误告诉我我们没有安装 pip3（Python）。我们可以通过将其添加到我们的 common tasks 中来修复这个问题。

![](Images/Day68_config6.png)

我们修复了上述问题并再次运行了 playbook，我们得到了一个成功的更改。

![](Images/Day68_config7.png)

我们可能应该确保我们新配置的 db01 服务器上的一切都如我们所愿。我们可以从控制节点使用 `ssh db01` 命令来做到这一点。

要连接 mySQL，我使用了 `sudo /usr/bin/mysql -u root -p`，并在提示时输入了 vagrant 作为 root 密码。

连接后，让我们首先确保我们创建了名为 devops 的用户。`select user, host from mysql.user;`

![](Images/Day68_config8.png)

现在我们可以执行 `SHOW DATABASES;` 命令来查看我们新创建的数据库。

![](Images/Day68_config9.png)

我实际上是用 root 连接的，但我们也可以用同样的方式使用我们的 devops 账户登录：`sudo /usr/bin/mysql -u devops -p`，但这里的密码是 DevOps90。

我发现，在我们的 `setup_mysql.yml` 中，我必须添加 `login_unix_socket: /var/run/mysqld/mysqld.sock` 这一行才能成功连接到我的 db01 mysql 实例，现在每次我运行这个时，它在创建用户时都会报告一个更改，如果有任何建议，我将不胜感激。

## 相关资源

- [What is Ansible](https://www.youtube.com/watch?v=1id6ERvfozo)
- [Ansible 101 - Episode 1 - Introduction to Ansible](https://www.youtube.com/watch?v=goclfp6a2IQ)
- [NetworkChuck - You need to learn Ansible right now!](https://www.youtube.com/watch?v=5hycyr-8EKs&t=955s)
- [Your complete guide to Ansible](https://www.youtube.com/playlist?list=PLnFWJCugpwfzTlIJ-JtuATD2MBBD7_m3u)

上面列出的最后一个播放列表是本节大部分代码和想法的来源，是一个非常棒的资源，以视频格式进行讲解。

[第69天](day69.md)见。
