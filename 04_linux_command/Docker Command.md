# <font color=#69D600>Docker Command</font>

[TOC]

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







### DONE



