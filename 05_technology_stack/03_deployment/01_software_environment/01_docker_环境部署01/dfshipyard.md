# <font color=#69D600>shipyard Dockerfile</font>

[TOC]

#### Version: shipyard

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 



### docker pull

```
cd /home/Software/Shipyard

docker pull alpine
docker pull swarm
docker pull rethinkdb
docker pull microbox/etcd
docker pull shipyard/docker-proxy
docker pull shipyard/shipyard


```



### docker run

> Note: 
>
> 注意顺序，有容器依赖关系
>
> <ip4-addr> 对应主机 ip4 地址
>
> 检查 hostname 是否正常

```
# demo
docker run -itd --restart=always --name shipyard-rethinkdb rethinkdb
docker run -itd --restart=always -p 4001:4001 -p 7001:7001 \
 --name shipyard-discovery microbox/etcd:latest --name discovery
docker run -itd --restart=always -p 2375:2375 --hostname=$HOSTNAME \
 -v /var/run/docker.sock:/var/run/docker.sock -e PORT=2375 \
 --name shipyard-proxy shipyard/docker-proxy:latest
docker run -itd --restart=always --name shipyard-swarm-manager swarm:latest \
 manage --host tcp://0.0.0.0:3375 etcd://<ip4-addr>:4001
docker run -itd --restart=always --name shipyard-swarm-agent swarm:latest \
 join --addr <ip4-addr>:2375 etcd://<ip4-addr>:4001
docker run -itd --restart=always -p 8080:8080 \
 --link shipyard-rethinkdb:rethinkdb --link shipyard-swarm-manager:swarm \
 --name shipyard-controller shipyard/shipyard:latest server -d tcp://swarm:3375

# docker run
docker run -itd --restart=always --name shipyard-rethinkdb rethinkdb
docker run -itd --restart=always -p 4001:4001 -p 7001:7001 \
 --name shipyard-discovery microbox/etcd:latest --name discovery
docker run -itd --restart=always -p 2375:2375 --hostname=$HOSTNAME \
 -v /var/run/docker.sock:/var/run/docker.sock -e PORT=2375 \
 --name shipyard-proxy shipyard/docker-proxy:latest
docker run -itd --restart=always --name shipyard-swarm-manager swarm:latest \
 manage --host tcp://0.0.0.0:3375 etcd://121.37.190.139:4001
docker run -itd --restart=always --name shipyard-swarm-agent swarm:latest \
 join --addr 121.37.190.139:2375 etcd://121.37.190.139:4001
docker run -itd --restart=always -p 8080:8080 \
 --link shipyard-rethinkdb:rethinkdb --link shipyard-swarm-manager:swarm \
 --name shipyard-controller shipyard/shipyard:latest server -d tcp://swarm:3375

```



#### 防火墙相关

```
iptables -P FORWARD ACCEPT
iptables -nL

firewalld status

firewall-cmd --zone=public --add-port=22/tcp --permanent
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-port=2375/tcp --permanent
firewall-cmd --zone=public --add-port=3375/tcp --permanent
firewall-cmd --zone=public --add-port=4001/tcp --permanent
firewall-cmd --zone=public --add-port=7001/tcp --permanent

firewall-cmd --reload

firewall-cmd --zone=public --list-ports

firewall-cmd --state

firewall-cmd --get-default-zone

firewall-cmd --zone=public --query-service=ssh
firewall-cmd --permanent --zone=public --add-service=ssh
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --permanent --zone=public --add-service=https

# SELinux
sestatus
getenforce
setenforce 0

```



### 快速删除容器、镜像

​		测试报错时快速删除无用容器和镜像，以便重试，注意命令适用场景，慎用

```
# 删除所有容器，已有正常容器不适用，会删所有，慎用
docker rm -f $(docker ps -qa)
# 删除未打 dangling 标签的镜像，一般是无用的中间层镜像
docker rmi $(docker images -f "dangling=true" -q)

docker rm -f $(docker ps -qa)
docker rmi $(docker images -f "dangling=true" -q)

```









### 相关问题追查解决备注







### DONE