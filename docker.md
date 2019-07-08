## Docker 教程

### 安装Docker





#### Ubuntu Docker 安装

**前提条件**

```bash
# docker要求Ubuntu系统的内核版本高于3.10，通过 uname -r 命令查看当前内核版本
ubuntu@gq:~$ uname -r
4.4.0-154-generic
```

##### 获取最新版本Docker

```bash
ubuntu@gq:~$ sudo wget -qO- https://get.docker.com/ | sh
```

```bash
Client:
 Version:           18.09.7
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        2d0083d
 Built:             Thu Jun 27 17:56:17 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.7
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.8
  Git commit:       2d0083d
  Built:            Thu Jun 27 17:23:02 2019
  OS/Arch:          linux/amd64
  Experimental:     false
If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

  sudo usermod -aG docker ubuntu

Remember that you will have to log out and back in for this to take effect!

WARNING: Adding a user to the "docker" group will grant the ability to run
         containers which can be used to obtain root privileges on the
         docker host.
         Refer to https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface
         for more information.
```

```bash
If you would like to use Docker as a non-root user, you should now consider
adding your user to the "docker" group with something like:

# 当要以非root用户可以直接运行docker时，
# 需要执行 sudo usermod -aG docker ***(username) 命令，然后重新登陆
sudo usermod -aG docker ubuntu
```

##### 启动Docker后台服务

```bash
ubuntu@gq:~$ sudo service docker start 
ubuntu@gq:~$ ps -af | grep docker
ubuntu   14365 14284  0 03:24 pts/0    00:00:00 grep --color=auto docker
```

##### 镜像加速

鉴于国内网络问题，后续拉取 Docker 镜像十分缓慢，需要配置加速器来解决

使用的是网易的镜像地址：`http://hub-mirror.c.163.com`

```json
# 新版的 Docker 使用 /etc/docker/daemon.json (没有改文件，先创建一个)
{
    "registry-mirrors": ["http://hub-mirror.c.163.com"]
}

```



### Docker 使用

#### Docker Hello World

Docker 允许在容器内运行应用程序， 使用 **docker run** 命令来在容器内运行一个应用程序。

```bash
ubuntu@gq:~$ docker run ubuntu:15.10 /bin/echo "Hello World"
Unable to find image 'ubuntu:15.10' locally
15.10: Pulling from library/ubuntu
7dcf5a444392: Pull complete 
759aa75f3cee: Pull complete 
3fa871dc8a2b: Pull complete 
224c42ae46e7: Pull complete 
Digest: sha256:02521a2d079595241c6793b2044f02eecf294034f31d6e235ac4b2b54ffc41f3
Status: Downloaded newer image for ubuntu:15.10
Hello World
```

- **docker:** Docker 的二进制执行文件。
- **run:**与前面的 docker 组合来运行一个容器。
- **ubuntu:15.10**指定要运行的镜像，Docker首先从本地主机上查找镜像是否存在，如果不存在，Docker 就会从镜像仓库 Docker Hub 下载公共镜像。
- **/bin/echo "Hello world":** 在启动的容器里执行的命令

##### 运行交互式的容器

通过docker的两个参数 -i -t，让docker运行的容器实现"对话"的能力

```bash
ubuntu@gq:~$ docker run -i -t ubuntu:15.10 /bin/bash
root@c23d970bee26:/#
```

- **-t:**在新容器内指定一个伪终端或终端。
- **-i:**允许你对容器内的标准输入 (STDIN) 进行交互。

尝试在容器中运行命令 **cat /proc/version**和**ls**分别查看当前系统的版本信息和当前目录下的文件列表

```bash
root@c23d970bee26:/# cat /proc/version
Linux version 4.4.0-98-generic (buildd@lcy01-03) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4) ) #121-Ubuntu SMP Tue Oct 10 14:24:03 UTC 2017
root@c23d970bee26:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

可以通过运行exit命令或者使用CTRL+D来退出容器。



##### 启动容器（后台模式）

使用以下命令创建一个以进程方式运行的容器

```bash
ubuntu@gq:~$ docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
ad09c8d2be4dd5d4a826ee72c9dbccada46f221c6c01f41c323138b81e1b6947
```

在输出中，没有看到期望的"hello world"，而是一串长字符

**2b1b7a428627c51ab8810d541d759f072b4fc75487eed05812646b8534a2fe63**

这个长字符串叫做容器ID，对每个容器来说都是唯一的，可以通过容器ID来查看对应的容器发生了什么。

首先，需要确认容器有在运行，可以通过 **docker ps** 来查看

```bash
ubuntu@gq:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
ad09c8d2be4d        ubuntu:15.10        "/bin/sh -c 'while t…"   56 seconds ago      Up 56 seconds                           keen_albattani
```

**CONTAINER ID:**容器ID

**NAMES:**自动分配的容器名称

在容器内使用docker logs命令，查看容器内的标准输出

```bash
ubuntu@gq:~$ docker logs ad09c8d2be4d
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
hello world
```

##### 停止容器

使用 **docker stop** 命令来停止容器:

```bash
ubuntu@gq:~$ docker stop ad09c8d2be4d
ad09c8d2be4d
```

通过docker ps查看，容器已经停止工作:

```bash
ubuntu@gq:~$ docker ps
CONTAINER ID   IMAGE   COMMAND   CREATED   STATUS  PORTS  NAMES
```

### Docker 容器使用

#### Docker 客户端

docker 客户端非常简单 ,可以直接输入 docker 命令来查看到 Docker 客户端的所有命令选项

```bash
ubuntu@gq:~$ docker

Usage:	docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/home/ubuntu/.docker")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
      --tls                Use TLS; implied by --tlsverify
      --tlscacert string   Trust certs signed only by this CA (default "/home/ubuntu/.docker/ca.pem")
      --tlscert string     Path to TLS certificate file (default "/home/ubuntu/.docker/cert.pem")
      --tlskey string      Path to TLS key file (default "/home/ubuntu/.docker/key.pem")
      --tlsverify          Use TLS and verify the remote
  -v, --version            Print version information and quit

Management Commands:
  builder     Manage builds
  config      Manage Docker configs
  container   Manage containers
  engine      Manage the docker engine
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins 
  ......
```

可以通过命令 **docker command --help** 更深入的了解指定的 Docker 命令使用方法

例如要查看 **docker stats** 指令的具体使用方法：

```bash
ubuntu@gq:~$ docker stats --help

Usage:	docker stats [OPTIONS] [CONTAINER...]

Display a live stream of container(s) resource usage statistics

Options:
  -a, --all             Show all containers (default shows just running)
      --format string   Pretty-print images using a Go template
      --no-stream       Disable streaming stats and only pull the first result
      --no-trunc        Do not truncate output
```

##### 运行一个web应用

在docker容器中运行一个 Python Flask 应用来运行一个web应用。

```bash
ubuntu@gq:~$ docker pull training/webapp  # 载入镜像
ubuntu@gq:~$ docker run -d -P training/webapp python app.py
```

- **-d:**让容器在后台运行。
- **-P:**将容器内部使用的网络端口映射到我们使用的主机上。

##### 查看web应用容器

```bash
ubuntu@gq:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
1889d62415e7        training/webapp     "python app.py"     40 seconds ago      Up 39 seconds       0.0.0.0:32768->5000/tcp   quirky_noyce
```

这里多了端口信息。

Docker 开放了 5000 端口（默认 Python Flask 端口）映射到主机端口 32768上。

可以通过浏览器访问WEB应用

可以通过 -p 参数来设置不一样的端口

```bash
ubuntu@gq:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
73adbf51d1fc        training/webapp     "python app.py"     5 seconds ago       Up 3 seconds        0.0.0.0:5000->5000/tcp    hardcore_liskov
1889d62415e7        training/webapp     "python app.py"     3 minutes ago       Up 3 minutes        0.0.0.0:32768->5000/tcp   quirky_noyce
```

容器内部的 5000 端口映射到我们本地主机的 5000 端口上。

##### 查看web应用程序日志

docker logs [ID或者名字] 可以查看容器内部的标准输出。

```bash
ubuntu@gq:~$ docker logs -f 1889d62415e7
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
192.168.0.134 - - [08/Jul/2019 03:48:57] "GET / HTTP/1.1" 200 -
192.168.0.134 - - [08/Jul/2019 03:48:57] "GET /favicon.ico HTTP/1.1" 404 -
```

**-f:** 让 **docker logs** 像使用 **tail -f** 一样来输出容器内部的标准输出。

##### 查看web应用程序容器的进程

``` bash
ubuntu@gq:~$ docker top hardcore_liskov 
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                16237               16214               0                   03:51               ?                   00:00:00            python app.py
```

##### 检查web应用程序

使用 **docker inspect** 来查看 Docker 的底层信息。它会返回一个 JSON 文件记录着 Docker 容器的配置和状态信息。

```bash
ubuntu@gq:~$ docker inspect hardcore_liskov
```

##### 重启web应用程序

已经停止的容器，可以使用命令 docker start 来启动。

```bash
ubuntu@gq:~$ docker restart hardcore_liskov 
hardcore_liskov
```

docker ps -l 查询最后一次创建的容器：

```bash
ubuntu@gq:~$ docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
73adbf51d1fc        training/webapp     "python app.py"     9 minutes ago       Up 59 seconds       0.0.0.0:5000->5000/tcp   hardcore_liskov
```

##### 移除web应用容器

```bash
ubuntu@gq:~$ docker rm hardcore_liskov 
hardcore_liskov
```

删除容器时，容器必须是停止状态，否则会报错误。





#### Docker 镜像使用

​	当运行容器时，使用的镜像如果在本地中不存在，docker 就会自动从 docker 镜像仓库中下载，默认是从 Docker Hub 公共镜像源下载。

##### 列出镜像列表

```bash
ubuntu@gq:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              15.10               9b9cb95443b5        2 years ago         137MB
training/webapp     latest              6fae60ef3446        4 years ago         349MB
```

- **REPOSITORY：**表示镜像的仓库源
- **TAG：**镜像的标签
- **IMAGE ID：**镜像ID
- **CREATED：**镜像创建时间
- **SIZE：**镜像大小

##### 获取一个新的镜像

```bash
ubuntu@gq:~$ docker pull ubuntu:13.10
13.10: Pulling from library/ubuntu
a3ed95caeb02: Pull complete 
0d8710fc57fd: Pull complete 
5037c5cd623d: Pull complete 
83b53423b49f: Pull complete 
e9e8bd3b94ab: Pull complete 
7db00e6b6e5e: Pull complete 
Digest: sha256:403105e61e2d540187da20d837b6a6e92efc3eb4337da9c04c191fb5e28c44dc
Status: Downloaded newer image for ubuntu:13.10
```

##### 查找镜像

```bash
ubuntu@gq:~$ docker search httpd
```

**NAME:**镜像仓库源的名称

**DESCRIPTION:**镜像的描述

**OFFICIAL:**是否docker官方发布

下载完成后，我们就可以使用这个镜像了。

```bash
ubuntu@gq:~$ docker run httpd
```

##### 创建镜像

当从docker镜像仓库中下载的镜像不能满足需求时，可以通过以下两种方式对镜像进行更改。

- 1.从已经创建的容器中更新镜像，并且提交这个镜像
- 2.使用 Dockerfile 指令来创建一个新的镜像

##### 更新镜像

更新镜像之前，需要使用镜像来创建一个容器。

```bash
ubuntu@gq:~$ docker run -t -i ubuntu:15.10 /bin/bash
root@7e2c0e6cfc9c:/#
```

在运行的容器内使用 apt-get update 命令进行更新。

在完成操作之后，输入 exit命令来退出这个容器。

此时ID为7e2c0e6cfc9c的容器，是按需求更改的容器。可以通过命令 docker commit来提交容器副本。

```bash
ubuntu@gq:~$ docker commit -m='has update' -a='gq' 7e2c0e6cfc9c gq/ubuntu:v2
sha256:c21c55a5847c704abc247effbdb16c36c915e178073ab1f5b6839fe7422249a6
```

- **-m:**提交的描述信息
- **-a:**指定镜像作者
- **7e2c0e6cfc9c：**容器ID
- **gq/ubuntu:v2:**指定要创建的目标镜像名



可以使用 **docker images** 命令来查看新镜像 **gq/ubuntu:v2**：

```bash
ubuntu@gq:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
gq/ubuntu           v2                  c21c55a5847c        About a minute ago   137MB
ubuntu              15.10               9b9cb95443b5        2 years ago          137MB
training/webapp     latest              6fae60ef3446        4 years ago          349MB
ubuntu              13.10               7f020f7bf345        5 years ago          185MB
```

使用的新镜像 **gq/ubuntu** 来启动一个容器

```bash
ubuntu@gq:~$ docker run -t -i gq/ubuntu:v2 /bin/bash
root@8deedd047c6b:/#
```



##### 构建镜像

使用命令 **docker build** ， 从零开始来创建一个新的镜像。为此，需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建镜像。

```bash
ubuntu@gq:~$ cat Dockerfile 
FROM    centos:6.7
MAINTAINER      Fisher "gaoqiang0807@gmail.com"

RUN     /bin/echo 'root:123456' |chpasswd
RUN     useradd centos
RUN     /bin/echo 'centos:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
```

每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。

第一条FROM，指定使用哪个镜像源

RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。

然后，使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像

```bash
ubuntu@gq:~$ sudo docker build -t gq/centos:6.7 .
Sending build context to Docker daemon  15.87kB
Step 1/9 : FROM    centos:6.7
6.7: Pulling from library/centos
cbddbc0189a0: Pull complete 
Digest: sha256:4c952fc7d30ed134109c769387313ab864711d1bd8b4660017f9d27243622df1
Status: Downloaded newer image for centos:6.7
 ---> 9f1de3c6ad53
Step 2/9 : MAINTAINER      Fisher "gaoqiang0807@gmail.com"
 ---> Running in 5c6559e8bd46
Removing intermediate container 5c6559e8bd46
 ---> a8896110d8cf
Step 3/9 : RUN     /bin/echo 'root:123456' |chpasswd
 ---> Running in 4df15f5e087a
Removing intermediate container 4df15f5e087a
 ---> c7ea1f9a50d8
Step 4/9 : RUN     useradd centos
 ---> Running in 66ffd5c9be9a
Removing intermediate container 66ffd5c9be9a
 ---> 370eba11fc67
Step 5/9 : RUN     /bin/echo 'centos:123456' |chpasswd
 ---> Running in ea00126e63db
Removing intermediate container ea00126e63db
 ---> 7d1d9fed6d19
Step 6/9 : RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
 ---> Running in a5c83d27e76f
Removing intermediate container a5c83d27e76f
 ---> 5c83ef4d251b
Step 7/9 : EXPOSE  22
 ---> Running in e1dd70202018
Removing intermediate container e1dd70202018
 ---> fb5dc3f10e4b
Step 8/9 : EXPOSE  80
 ---> Running in 50354668d912
Removing intermediate container 50354668d912
 ---> fb83886d8361
Step 9/9 : CMD     /usr/sbin/sshd -D
 ---> Running in e842a625b2c9
Removing intermediate container e842a625b2c9
 ---> ec111fe98eeb
Successfully built ec111fe98eeb
Successfully tagged gq/centos:6.7
```

- **-t** ：指定要创建的目标镜像名
- **.** ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

可以使用新的镜像来创建容器

```bash
ubuntu@ubuntu-xenial:~$ docker run -t -i gq/centos:6.7 /bin/bash
[root@5f78b78a1009 /]# id centos
uid=500(centos) gid=500(centos) groups=500(centos)
```

##### 设置镜像标签

```bash
ubuntu@gq:~$ docker tag ec111fe98eeb gq/centos:dev
```

docker tag 镜像ID，这里是 ec111fe98eeb,用户名称、镜像源名(repository name)和新的标签名(tag)。

使用 docker images 命令可以看到，ID为ec111fe98eeb的镜像多一个标签。

```bash
ubuntu@gq:~$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
gq/centos           6.7                 ec111fe98eeb        12 minutes ago      191MB
gq/centos           dev                 ec111fe98eeb        12 minutes ago      191MB
gq/ubuntu           v2                  c21c55a5847c        18 minutes ago      137MB
centos              6.7                 9f1de3c6ad53        3 months ago        191MB
ubuntu              15.10               9b9cb95443b5        2 years ago         137MB
training/webapp     latest              6fae60ef3446        4 years ago         349MB
ubuntu              13.10               7f020f7bf345        5 years ago         185MB
```



#### Docker 容器连接

##### 网络端口映射

创建一个 python 应用的容器。

```bash
ubuntu@gq:~$ docker run -d -P training/webapp python app.py
a4652d98a2e8b0c1b88e6310375b2c1de444fc1d718b62e86a23c43d14e0d3be
```

使用 **-P** 参数创建一个容器，使用 **docker ps** 可以看到容器端口 5000 绑定主机端口 32768。

也可以使用 **-p** 标识来指定容器端口绑定到主机端口。

两种方式的区别是:

- **-P :**是容器内部端口**随机**映射到主机的高端口。
- **-p :** 是容器内部端口绑定到**指定**的主机端口。

```bash
ubuntu@gq:~$ docker run -d -p 5000:5000 training/webapp python app.py
94dbfca84c102a66a7f23a753ded2e14d6ce39bc7aba242b695ab71e5f1d137a
ubuntu@gq:~$ docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                     NAMES
94dbfca84c10        training/webapp     "python app.py"     3 seconds ago       Up 2 seconds        0.0.0.0:5000->5000/tcp    angry_shtern
a4652d98a2e8        training/webapp     "python app.py"     2 minutes ago       Up 2 minutes        0.0.0.0:32769->5000/tcp   jolly_driscoll
```

另外，可以指定容器绑定的网络地址，比如绑定 127.0.0.1

```bash
ubuntu@gq:~$ docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
541b6ae3203fafa04229da9333d12f1c3dbdc6412b4cd001934d082344a498dc
ubuntu@gq:~$ docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS                      NAMES
541b6ae3203f        training/webapp     "python app.py"     3 seconds ago        Up 2 seconds        127.0.0.1:5001->5000/tcp   tender_hypatia
94dbfca84c10        training/webapp     "python app.py"     About a minute ago   Up About a minute   0.0.0.0:5000->5000/tcp     angry_shtern
a4652d98a2e8        training/webapp     "python app.py"     3 minutes ago        Up 3 minutes        0.0.0.0:32769->5000/tcp    jolly_driscoll
```

这样就可以通过访问 127.0.0.1:5001 来访问容器的 5000 端口。



上面的例子中，默认都是绑定 tcp 端口，如果要绑定 UDP 端口，可以在端口后面加上 **/udp**。

```bash
ubuntu@gq:~$ docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py
0d9a0f232c9f141be2ffcdd48e04b926e9af275efc4a2285e5e0c23db6608d56
ubuntu@gq:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS                                NAMES
0d9a0f232c9f        training/webapp     "python app.py"     3 seconds ago        Up 2 seconds        5000/tcp, 127.0.0.1:5000->5000/udp   dazzling_visvesvaraya
541b6ae3203f        training/webapp     "python app.py"     About a minute ago   Up About a minute   127.0.0.1:5001->5000/tcp             tender_hypatia
a4652d98a2e8        training/webapp     "python app.py"     4 minutes ago        Up 4 minutes        0.0.0.0:32769->5000/tcp              jolly_driscoll
```

**docker port** 命令可以让我们快捷地查看端口的绑定情况

```bash
ubuntu@gq:~$ docker port jolly_driscoll 
5000/tcp -> 0.0.0.0:32769
```



##### Docker 容器连接

端口映射并不是唯一把 docker 连接到另一个容器的方法。

docker 有一个连接系统允许将多个容器连接在一起，共享连接信息。

docker 连接会创建一个父子关系，其中父容器可以看到子容器的信息。

**容器命名**

创建一个容器的时候，docker 会自动对它进行命名。另外，也可以使用 **--name** 标识来命名容器，

例如：

```bash
ubuntu@gq:~$ docker run -d -P --name gq training/webapp python app.py
883f95a8df2cb17575a081b0fcd5807274033ccc3e7c2eada8df4a73121f5342
ubuntu@gq:~$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                                NAMES
883f95a8df2c        training/webapp     "python app.py"     4 seconds ago       Up 3 seconds        0.0.0.0:32770->5000/tcp              gq
0d9a0f232c9f        training/webapp     "python app.py"     5 minutes ago       Up 5 minutes        5000/tcp, 127.0.0.1:5000->5000/udp   dazzling_visvesvaraya
541b6ae3203f        training/webapp     "python app.py"     6 minutes ago       Up 6 minutes        127.0.0.1:5001->5000/tcp             tender_hypatia
a4652d98a2e8        training/webapp     "python app.py"     9 minutes ago       Up 9 minutes        0.0.0.0:32769->5000/tcp              jolly_driscoll
```





#### Docker 安装 Nginx

查找Docker Hub上的 Nginx 镜像

```bash
ubuntu@gq:~$ docker search nginx
```

拉取官方的镜像

```bash
ubuntu@gq:~$ docker pull nginx
```

在本地镜像列表里查到 REPOSITORY 为 nginx 的镜像

```bash
ubuntu@gq:~$ docker images nginx
```

以下命令使用 NGINX 默认的配置来启动一个 Nginx 容器实例：

```bash
ubuntu@gq:~$ docker run --name gq-nginx-test -p 8081:80 -d nginx
```

- `gq-nginx-test` 容器名称。
- the `-d`设置容器在在后台一直运行。
- the `-p` 端口进行映射，将本地 8081 端口映射到容器内部的 80 端口。

可以使用 docker ps 命令查看容器是否有在运行：

```bash
ubuntu@gq:~$ docker ps
```

PORTS 部分表示端口映射，本地的 8081 端口映射到容器内部的 80 端口。

在浏览器中打开 **http://ip:8081/**

##### Nginx 部署

首先，创建目录 nginx, 用于存放后面的相关东西。

```bash
ubuntu@gq:~$ mkdir -p ~/nginx/www ~/nginx/logs ~/nginx/conf
```

拷贝容器内 Nginx 默认配置文件到本地当前目录下的 conf 目录，容器 ID 可以查看 docker ps 命令输入中的第一列：

```bash
ubuntu@gq:~$ docker cp 1ca77b3301f1:/etc/nginx/nginx.conf ~/nginx/conf/
```

- **www**: 目录将映射为 nginx 容器配置的虚拟目录。
- **logs**: 目录将映射为 nginx 容器的日志目录。
- **conf**: 目录里的配置文件将映射为 nginx 容器的配置文件。



##### 部署命令

```bash
ubuntu@gq:~$ docker run -d -p 8082:80 --name gq-nginx-test-web -v ~/nginx/www:/usr/share/nginx/html -v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v ~/nginx/logs:/var/log/nginx nginx
cecc65cce1c99a59b01da5ecf0953e90bfaed71b38184be1623b2890246824f2
```

- **-p 8082:80：** 将容器的 80 端口映射到主机的 8082 端口。
- **--name runoob-nginx-test-web：**将容器命名为gq-nginx-test-web。
- **-v ~/nginx/www:/usr/share/nginx/html：**将创建的 www 目录挂载到容器的 /usr/share/nginx/html。
- **-v ~/nginx/conf/nginx.conf:/etc/nginx/nginx.conf：**将创建的 nginx.conf 挂载到容器的 /etc/nginx/nginx.conf。
- **-v ~/nginx/logs:/var/log/nginx：**将创建的 logs 挂载到容器的 /var/log/nginx。

启动以上命令后进入 ~/nginx/www 目录：

```bash
ubuntu@gq:~$ cd ~/nginx/www
```

创建 index.html 文件，内容如下：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>教程</title>
</head>
<body>
    <h1>我的第一个标题</h1>
    <p>我的第一个段落。</p>
</body>
</html>
```

在浏览器中打开 **http://ip:8082/**

##### 相关命令

如果要重新载入 NGINX 可以使用以下命令发送 HUP 信号到容器：

```bash
ubuntu@gq:~/nginx/www$ docker kill -s HUP container-name
container-name
```

##### 重启Nginx容器命令

```bash
ubuntu@gq:~/nginx/www$ docker restart container-name
container-name
```



#### Docker 安装 PHP

```bash
ubuntu@gq:~$ docker search php
ubuntu@gq:~$ docker pull php:5.6-fpm
ubuntu@gq:~$ docker images
```

启动PHP

```bash
ubuntu@gq:~$ docker run --name php-fpm -v ~/nginx/www:/www -d php:5.6-fpm 
```

- **--name php-fpm** : 将容器命名为 php-fpm。
- **-v ~/nginx/www:/www** : 将主机中项目的目录 www 挂载到容器的 /www

创建 ~/nginx/conf/conf.d 目录

```bash
ubuntu@gq:~$ mkdir ~/nginx/conf/conf.d
```

在该目录下添加 **~/nginx/conf/conf.d/gq-test-php.conf** 文件，内容如下：

```bash
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm index.php;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /www/$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```

配置文件说明：

- **php:9000**: 表示 php-fpm 服务的 URL，下面我们会具体说明。
- **/www/**: 是 **php-fpm** 中 php 文件的存储路径，映射到本地的 ~/nginx/www 目录。



启动Nginx：

```bash
ubuntu@gq:~$ docker run --name gq-php-nginx -p 8083:80 -d \
-v ~/nginx/www:/usr/share/nginx/html:ro \
-v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \ 
--link php-fpm:php nginx
```

- **-p 8083:80**: 端口映射，把 **nginx** 中的 80 映射到本地的 8083 端口。
- **~/nginx/www**: 是本地 html 文件的存储目录，/usr/share/nginx/html 是容器内 html 文件的存储目录。
- **~/nginx/conf/conf.d**: 是本地 nginx 配置文件的存储目录，/etc/nginx/conf.d 是容器内 nginx 配置文件的存储目录。
- **--link php-fpm:php**: 把 **php-fpm** 的网络并入 **nginx**，并通过修改 **nginx** 的 /etc/hosts，把域名 **php** 映射成 127.0.0.1，让 nginx 通过 php:9000 访问 php-fpm。



接下来在 ~/nginx/www 目录下创建 index.php，代码如下：

```php
<?php
echo phpinfo();
?>
```

浏览器打开 **http://ip:8083/index.php**：



#### Docker 安装 Mysql

```bash
ubuntu@gq:~$ docker pull mysql:5.6
```

运行容器

```bash
ubuntu@gq:~$ docker run -p 3306:3306 --name mysql -v $PWD/conf:/etc/mysql/conf.d -v $PWD/logs:/logs -v $PWD/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
```

- **-p 3306:3306**：将容器的 3306 端口映射到主机的 3306 端口。
- **-v  $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。
- **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs。
- **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。
- **-e MYSQL_ROOT_PASSWORD=123456：**初始化 root 用户的密码。

