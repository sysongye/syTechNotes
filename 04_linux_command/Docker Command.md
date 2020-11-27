# <font color=#69D600>Docker Command</font>

[TOC]

```perl
# 拉取 FastFDS
docker pull morunchang/fastdfs

# 运行 tracker
docker run -d --name tracker --net=host morunchang/fastdfs sh tracker.sh

# 运行 storage
docker run -d --name storage --net=host -e TRACKER_IP=192.168.100.5:22122 -e GROUP_NAME=group1 morunchang/fastdfs sh storage.sh

# 容器自启动
docker update --restart=always tracker
docker update --restart=always storage

# 停止防火墙服务
systemctl stop firewalld.service

# 显示已有镜像
docker images

# 显示已有容器信息
docker ps

```







### DONE



