# <font color=#69D600>Docker Command</font>

[TOC]

平台：Linux
```perl
# 拉取 FastFDS
docker pull morunchang/fastdfs

# 运行 tracker
docker run -d --name tracker --net=host morunchang/fastdfs sh tracker.sh

# 运行 storage
docker run -d --name storage --net=host -e TRACKER_IP=192.168.100.5:22122 -e GROUP_NAME=group1 morunchang/fastdfs sh storage.sh

# MySQL
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=songye --restart=always -d mysql:5.7
docker run -p 3306:3306 --name mysql \
-v /home/docker/mysql/conf:/etc/mysql \
-v /home/docker/mysql/logs:/var/log/mysql \
-v /home/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=songye \
--restart=always -d mysql:5.7

# Redis
docker run -itd --name redis --restart=always -p 6379:6379 redis

# 容器自启动
docker update --restart=always tracker
docker update --restart=always storage

# 停止防火墙服务
systemctl stop firewalld.service

# 显示已有镜像
docker images

# 显示已有容器信息
docker ps
docker container ls
docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}"
docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Command}}\t{{.RunningFor}}\t{{.Status}}"
docker ps -a --no-trunc --format "table {{.Names}}\t{{.Command}}\t{{.Ports}}"
docker ps -a --no-trunc --format "table {{.Names}}\t{{.Mounts}}"

# 删除容器 
docker rm 容器ID
docker rm -f 容器ID
docker rm -f 容器ID1 容器ID2 中间空格隔开

# 删除所有容器
docker rm -f $(docker ps -qa)

# 进入容器
docker exec -it mysql bash
mysql -u username -p password

CREATE DATABASE `eladmin` CHARACTER SET 'utf8' COLLATE 'utf8_general_ci';
use eladmin;
source /etc/mysql/eladmin.sql
```

```perl
-format="TEMPLATE"
Pretty-print containers using a Go template.
Valid placeholders:
.ID - Container ID
.Image - Image ID
.Command - Quoted command
.CreatedAt - Time when the container was created.
.RunningFor - Elapsed time since the container was started.
.Ports - Exposed ports.
.Status - Container status.
.Size - Container disk size.
.Names - Container names.
.Labels - All labels assigned to the container.
.Label - Value of a specific label for this container. For example {{.Label "com.docker.swarm.cpu"}}
.Mounts - Names of the volumes mounted in this container.

docker ps -a --no-trunc --format "table {{.Names}}\t{{.Mounts}}"

```







### DONE



