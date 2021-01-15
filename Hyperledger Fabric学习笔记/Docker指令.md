# 查看所有镜像

```BASH
docker images
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
```

