# 使用场景

**docker-compose用来在单机上编排容器（定义和运行多个容器，使容器能互通）**

**Eg：前端和后端部署在一台机器上，现在直接通过编写docker-compose文件对多个服务（可定义依赖，按顺序启动服务）同时进行启动/停止/更新**

> **注：**
>
> docker-compose将所管理的容器分为3层结构：project service container
>
> `docker-compose.yml`组成一个project，**project**里**包括**多个**service**，每个service定义了容器运行的镜像（或构建镜像），网络端口，文件挂载，参数，依赖等，每个**service**可包括同一个镜像的多个容器实例。
>
> 即 project 包含 service ，service 包含 container 

# 命令

```BASH
# 启动所有容器 -d detach 后台运行
docker-compose -f docker-compose.yaml up -d

# 查看容器日志 -f, --follow         跟踪实时日志
docker logs -f [容器名称]

# 停止运行的service
docker-compose stop [serviceName]
# 删除已停止的所有service
docker-compose rm -f [serviceName]

# 停止并移除整个project的所有services
## -v ：删除挂载卷和volunme的链接
docker-compose down -v（相当于 stop + rm ）：

# 显示所有容器
docker-compose ps

# 验证（docker-compose.yml）文件配置，当配置正确时，不输出任何内容，当文件配置错误，输出错误信息。
docker-compose config  -q

```





# 编写`docker-compose.yml`

官网地址：https://docs.docker.com/compose/compose-file/

```yml
version: '3'
services:
  back:
    image: backService:1.0
    container_name: back
    environment:
      - name=tom
      - DB_PATH=jdbc:sqlite:/data/ns.db
    restart: always
    privileged: true
    ports:
      - "9000:9000"
    networks:
      - "net"
    volumes:
      - "/root/k3s.kube.config:/k3s.kube.config"
      - "/root/data:/data"
      - "/etc/network/interfaces:/etc/network/interfaces"
  front:
    image: front:1.0
    container_name: front
    restart: always
    ports:
      - "10087:80"
    networks:
      - "net"
    volumes:
      - "/root/nginx.conf:/etc/nginx/nginx.conf"
networks:
  net:
    driver: bridge
```

* **version**：指定 `docker-compose.yml `文件的写法格式

* **services**：多个容器集合
  * **environment**：环境变量配置，可以用数组或字典两种方式

    ```YML
    environment:
        RACK_ENV: "development"
        SHOW: "ture"
    -------------------------
    environment:
        - RACK_ENV="development"
        - SHOW="ture"
    ```

  * **image**：指定服务所使用的镜像

    ```
    version: '2'
    services:
      back:
        image: redis:alpine
    ```

  * **ports**：定义宿主机端口和容器端口的映射，可使用宿主机IP+宿主机端口进行访问 **宿主机端口**:**容器端口**

    ```yml
    ports:   # 暴露端口信息  - "宿主机端口:容器暴露端口"
    - "5000" # 注：仅指定容器端口时，宿主机将会随机选择端口 
    - "8081:8080"
    ```

  * **volumes**：卷挂载路径，定义宿主机的目录/文件和容器的目录/文件的映射 **宿主机路径:容器路径**

    方法有二，绝对路径和卷标，以下为绝对路径，[参考](https://www.cnblogs.com/lori/archive/2018/10/24/9843190.html)

    ```yml
    volumes:
      - "/var/lib/mysql"
      - "hostPath:containerPath"
      - "root/configs:/etc/configs"
    ```

  * **networks**：表示要加入的网络，需要引用第一层级`networks`下的条目

    ```YML
    services:
      back:
        ......
        ports:
          - "9000:9000"
        networks:
          - "net"
    ........
    networks:
      net:
        driver: bridge
    ```

  * **depends_on**：按顺序启动容器，默认情况下 compose 启动容器的顺序是不确定的，但是有些场景下我们希望能够控制容器的启动顺序，比如应该让运行数据库的程序先启动。我们可以通过 depends_on 来解决有依赖关系的容器的启动顺序问题，看下面的 demo：[参考](https://www.cnblogs.com/sparkdev/p/9803554.html)

    ```YML
    version: '3'
    services:
      proxy:
        image: nginx
        ports:
          - "80:80"
        depends_on:
          - webapp
          - redis
      webapp:
        build: .
        depends_on:
          - redis
      redis:
        image: redis
    ```

    ![img](MarkdownAssets/docker-compose%E8%AF%A6%E8%A7%A3.assets/952033-20181017131603316-913957749.png)

    无论我们执行多少次这样的启动操作，这三个容器的启动顺序都是不变的。如果不应用 depends_on，每次执行 up 命令容器的启动顺序可能都是不一样的。
    需要注意的是 depends_on 只是解决了控制容器启动顺序的问题，如果一个容器的启动时间非常长，后面的容器并不会等待它完成启动。如果要解决这类问题(等待容器完成启动并开始提供服务)，需要使用 wait-for-it 等工具。

# Q&A

**注意volumes下挂载的路径要统一权限**

```YML
services:
    orderer.dy:
        container_name: orderer
        ......
        volumes:
          - type: bind
            source: ./orderer/orderer-genesis.block
            target: /etc/hyperledger/fabric/orderer/orderer.genesis.block
            read_only: false
          - type: bind
            source: ./fabricconfig
            target: /etc/hyperledger/fabric
            read_only: false
```

如上方两个挂载，不能一个设为`read_only: false`，另一个为`read_only: true`，这样会导致冲突！