# <font color=#69D600>Linux</font>

[TOC]

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7



## 简介

​		基于 Docker 部署的 Spring Cloud 开发环境



### 整体目录结构

```
/home/Software/
  |- CentOS
    |- Python
  |- Elasticsearch
  |- JDK
  |- Jenkins
  |- Kafka
  |- Kibana
  |- Logstash
  |- Mycat
  |- MySQL
  |- Nexus
  |- Nginx
  |- RabbitMQ
  |- Redis
  |- Shipyard
  |- Sonar
  |- Tengine
  |- Tomcat

mkdir -p /home/Software/CentOS/Python
mkdir -p /home/Software/Elasticsearch
mkdir -p /home/Software/JDK
mkdir -p /home/Software/Jenkins
mkdir -p /home/Software/Kafka
mkdir -p /home/Software/Kibana
mkdir -p /home/Software/Logstash
mkdir -p /home/Software/Mycat
mkdir -p /home/Software/MySQL
mkdir -p /home/Software/Nexus
mkdir -p /home/Software/Nginx
mkdir -p /home/Software/RabbitMQ
mkdir -p /home/Software/Redis
mkdir -p /home/Software/Shipyard
mkdir -p /home/Software/Sonar
mkdir -p /home/Software/Tengine
mkdir -p /home/Software/Tomcat

```



### 基础镜像环境

​		用于后续服务环境所依赖运行环境的基层镜像

#### 		Centos8	centos8:8

#### 		JDK	os8jdk8:291

#### 		Tomcat	os8tomcat:9

#### 		Nginx	os8nginx:1.20



```
centos                  latest    300e315adb2f   7 months ago     209MB
alpine                  latest    d4ff818577bc   5 weeks ago      5.6MB
rethinkdb               latest    3f37e5daf5bd   2 months ago     131MB
swarm                   latest    1a5eb59a410f   10 months ago    12.7MB
shipyard/shipyard       latest    36fb3dc0907d   4 years ago      58.8MB
shipyard/docker-proxy   latest    cfee14e5d6f2   5 years ago      9.47MB
microbox/etcd           latest    6aef84b9ec5a   5 years ago      17.9MB
```



docker network create --subnet=192.168.10.0/24 envsy



### 基础服务环境

#### Shipyard

##### 	docker pull alpine

##### 	docker pull swarm

##### 	docker pull rethinkdb

##### 	docker pull microbox/etcd

##### 	docker pull shipyard/docker-proxy

##### 	docker pull shipyard/shipyard



#### 		Elasticsearch	jdk8elksearch:7.13

#### 		Jenkins	cat9jenkins:2.289

#### 		Kafka	jdk8kafka:2.8

#### 		Kibana	jdk8elkkibana:7.13

#### 		Logstash	jdk8elklogstash:7.13

#### 		Mycat	jdk8mycat:1.6.7

#### 		MySQL	os8mysql:5.7.32

#### 		Nexus	jdk8nexus:3.32

#### 		RabbitMQ	jdk8rabbitmq:3.8.16

#### 		Redis	os8redis:6.2

#### 		Sonar	jdk8sonar:8.9

#### 		Tengine	os8tengine:2.3



#### Oracle	docker pull tinht/oracle-19c-ee

docker run -d -itp 1521 --name oracle tinht/oracle-19c-ee /bin/bash



docker exec -it oracle /bin/bash



### Docker Compose

```
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose

ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

docker-compose --version

```





### DONE