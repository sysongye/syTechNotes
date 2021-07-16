# <font color=#69D600>Linux</font>

[TOC]

### 简介

​		基于 Docker 部署的 Spring Cloud 开发环境



#### 整体目录结构

```
/root/software
 |- dkfile
 |- jdk
 |- mysql
 |- python
```



#### 基础镜像环境

​		用于后续服务环境所依赖运行环境的基层镜像

##### 		centos8	centos8:8

##### 		JDK	os8jdk8:291

##### 		tomcat

##### 		nginx



docker network create --subnet=192.168.10.0/24 envsy



#### 基础服务环境

##### Shipyard

```
docker pull alpine
docker pull swarm
docker pull rethinkdb
docker pull microbox/etcd
docker pull shipyard/docker-proxy
docker pull shipyard/shipyard

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

iptables -P FORWARD ACCEPT
iptables -nL

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



##### 		Elasticsearch

##### 		Jenkins

##### 		Kafka

##### 		Kibana

##### 		Logstash

##### 		Mycat

##### 		MySQL

##### 		Nexus

##### 		RabbitMQ

##### 		Redis

##### 		Sonar

##### 		Tengine









### DONE