# 查看所有镜像

```BASH
docker images
```

# 容器相关

## [启动容器](https://www.runoob.com/docker/docker-run-command.html)

```BASH
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
# OPTIONS说明
-d: 后台运行容器，并返回容器ID；
-i: 以交互模式运行容器，通常与 -t 同时使用；
-t: 为容器重新分配一个伪输入终端；
--name="nginx-lb": 为容器指定一个名称，或用空格隔开:--name nginx-lb;
-P: 随机端口映射，容器内部端口随机映射到主机的端口;
-p: 指定端口映射，格式为：主机(宿主)端口:容器端口;
--volume , -v: 绑定一个卷

```

**实例：**

```BASH
# 使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx。
docker run --name mynginx -d nginx:latest

# 使用镜像nginx:latest以后台模式启动一个容器,并将容器的80端口映射到主机随机端口。
docker run -P -d nginx:latest

# 使用镜像 nginx:latest，以后台模式启动一个容器,将容器的 80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data。
docker run -p 80:80 -v /data:/data -d nginx:latest

# 使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令。
docker run -it nginx-latest /bin/bash
```



## 执行命令

在运行的容器中执行命令，操作的对象是 **容器**。

```bash
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
# OPTIONS说明：
-d, --detach:分离模式: 在后台运行
-i, --interactive:即使没有附加也保持STDIN 打开
-t, --tty:分配一个伪终端(pseudo-tty)
```

比如想在容器上执行一个交互式的bash shell

```BASH
# 如：在容器 mynginx 中开启一个交互模式的终端:
docker exec -it mynginx bash # bash是指进入容器的终端，可执行shell命令
```



## 查看容器

```BASH
docker ps [options]
# -a :显示所有的容器，包括未运行的。
# -f :根据条件过滤显示的内容。
# --format :指定返回值的模板文件。
# -l :显示最近创建的容器。
# -n :列出最近创建的n个容器。
# --no-trunc :不截断输出。
# -q :静默模式，只显示容器编号。
# -s :显示总的文件大小。
```

输出详情介绍：
CONTAINER ID: 容器 ID。
IMAGE: 使用的镜像。
COMMAND: 启动容器时运行的命令。
CREATED: 容器的创建时间。
STATUS: 容器状态。
状态有7种：

- created（已创建）
- restarting（重启中）
- running（运行中）
- removing（迁移中）
- paused（暂停）
- exited（停止）
- dead（死亡）

PORTS: 容器的端口信息和使用的连接类型（tcp\udp）。
NAMES: 自动分配的容器名称。

# 获取容器/镜像的元数据

```bash
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
# -f :指定返回值的模板文件。
# -s :显示总的文件大小。
# --type :为指定类型返回JSON。
```

# 删除镜像

1. 停止所有的container，这样才能够删除其中的images：

```bash
docker stop $(docker ps -a -q)
```

2. 此时可以删除特定的镜像

```bash
docker rmi -f image_ID
```

**删除所有镜像文件**

```bash
docker rmi -f $(docker images -q)
# rmi: remove images
# rm: remove containers
```

