# docker基本使用
```
// 拉取镜像
docker pull redis
// 创建容器
docker run 容器id /bin/bash
// 停止容器
docker stop 容器id
// 启动容器
docker start 容器id
// 查看所有容器
docker ps -a
// 查看运行中的容器
docker ps
// 查看容器信息
docker inspect 容器id
// 进入容器
docker exec -it
// 查看镜像
docker images
// 删除镜像
docker rmi 镜像号
// 删除容器
docker rm -f 容器号
// 停止所有容器
docker stop $(docker ps -a -q)
// 删除所有容器
docker rm $(docker ps -a -q)
```

Q：我一套服务为什么不放在一个容器里面（Java、MySQL、Nginx、Redis等）？

A：因为既要维护容器内网络，又要维护端口等等之类的东西，Docker就是为了快速搭建环境而生的，而且Docker最好也是一个服务一个容器，这样好打理。

运行镜像的完整命令

```
docker run \
-d \ # 后台运行
--name redis6 \ # 自定义名字
-p 6379:6379 \ # 端口映射
redis # 镜像名称
docker run -d --name redis6 -p 6379:6379 redis redis-server --appendonly yes --requirepass "123456" # 完整命令
```

run和start的区别：

- run是创建一个新的容器
- start是把已经创建好的容器启动



**挂载介绍**

容器里面的文件都是在容器内部，而跟你当前电脑是没有关系的，如果删除了容器怎么办？但是资料又想保存就像MySQL一样，我只是换一台电脑就要把整个容器复制过去，太麻烦了！所以需要把容器的文件跟当前主机文件作为一个映射。

**命令教程**

参数-v宿主机路径:容器路径

```
# 以MySQL为例子docker run -d --name mysql8 -p 3306:3306 -v /data/mysql8/config:/etc/mysql/conf.d -v /data/mysql8/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql
# 以上的命令可以参考：https://hub.docker.com/_/mysql，里面有详细介绍
```

**容器之间怎么进行通讯**

容器虽然是能相互通讯的，但是容器每次重启ip都跟上一次不一样，所以这样通讯会很复杂。

**示范**

```
# 先拉个CentOS镜像下来docker pull centos# 创建个容器docker run -d -it --name centos1 centosdocker run -d -it --name centos2 centosdocker inspect centos1_id
```

**解决问题**

```
docker network create centos-networkdocker run -d -it --network centos-network --name centos3 centosdocker run -d -it --network centos-network --name centos4 centosdocker exec -it centos3_id /bin/bashping centos4 # 所以当创建了一个network后 容器都能加入到这个网络里面，很方便
```

这样就可以解决每次容器重启后ip不一致问题。