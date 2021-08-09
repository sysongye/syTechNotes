# <font color=#69D600>centos8:8 Dockerfile</font>

[TOC]

#### Version: centos8:8

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 需要先拉取一个 centos 镜像作为基础



### docker pull centos

​		创建基础系统镜像，作为 centos8:8 的基础，目前最新 centos 版本为 8.x

```
docker pull centos
```



### Dockerfile

​		基础镜像 `centos8:8` 的 Dockerfile

​		Filename: dfcentos8

​		合理利用 &&，减少 RUN 生成中间层镜像。

​		使用 ADD 可直接解压好压缩文件

​		注意 COPY 和 ADD 适用场景，使用 history 分析中间层大小，合理利用可以使镜像更小

> Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```perl
cd /home/Software

# docker-compose.yml
cat > docker-compose.yml
version: '3.9'
services:
  # 1 centos
  centos:
    image: centos8:8
    container_name: centos8
    build:
      context: /home/Software/CentOS
      dockerfile: dfcentos8
    deploy:
      resources:
        limits:
          memory: 300M
  # 2 os8jdk8
  os8jdk8:
    image: os8jdk8:291
    container_name: os8jdk8
    build:
      context: /home/Software/JDK
      dockerfile: dfos8jdk8
    deploy:
      resources:
        limits:
          memory: 300M
  # 3 jdk8tomcat
  jdk8tomcat:
    image: jdk8tomcat:9
    container_name: jdk8tomcat9
    build:
      context: /home/Software/Tomcat
      dockerfile: dfjdk8tomcat
    ports:
      - "8180:8080"
    deploy:
      resources:
        limits:
          memory: 300M
  # 4 jdk8elksearch
  jdk8elksearch:
    image: jdk8elksearch:7.13
    container_name: jdk8elksearch7
    build:
      context: /home/Software/Elasticsearch
      dockerfile: dfjdk8elksearch
    ports:
      - "9200:9200"
      - "9300:9300"
    deploy:
      resources:
        limits:
          memory: 300M
  # 5 cat9jenkins
  cat9jenkins:
    image: cat9jenkins:2.289
    container_name: cat9jenkins
    build:
      context: /home/Software/Jenkins
      dockerfile: dfcat9jenkins
    ports:
      - "8899:8080"
    deploy:
      resources:
        limits:
          memory: 1024M
  # 6 jdk8kafka:2.8
  jdk8kafka:
    image: jdk8kafka:2.8
    container_name: jdk8kafka
    build:
      context: /home/Software/Kafka
      dockerfile: dfjdk8kafka
    ports:
      - "9092:9092"
    deploy:
      resources:
        limits:
          memory: 300M
  # 7 jdk8elkkibana:7.13
  jdk8elkkibana:
    image: jdk8elkkibana:7.13
    container_name: jdk8elkkibana7
    build:
      context: /home/Software/Kibana
      dockerfile: dfjdk8elkkibana
    ports:
      - "5601:5601"
    deploy:
      resources:
        limits:
          memory: 300M
  # 8 jdk8elklogstash:7.13
  jdk8elklogstash:
    image: jdk8elklogstash:7.13
    container_name: jdk8elklogstash7
    build:
      context: /home/Software/Logstash
      dockerfile: dfjdk8elklogstash
    deploy:
      resources:
        limits:
          memory: 300M
  # 9 jdk8mycat:1.6.7
  jdk8mycat:
    image: jdk8mycat:1.6.7
    container_name: jdk8mycat
    build:
      context: /home/Software/Mycat
      dockerfile: dfjdk8mycat
    ports:
      - "8066:8066"
    deploy:
      resources:
        limits:
          memory: 300M
  # 10 os8mysql:5.7.32
  os8mysql:
    image: os8mysql:5.7.32
    container_name: os8mysql57
    build:
      context: /home/Software/MySQL
      dockerfile: dfos8mysql
    ports:
      - "3306:3306"
    deploy:
      resources:
        limits:
          memory: 512M
  # 11 jdk8nexus:3.32
  jdk8nexus:
    image: jdk8nexus:3.32
    container_name: jdk8nexus
    build:
      context: /home/Software/Nexus
      dockerfile: dfjdk8nexus
    ports:
      - "8081:8081"
    deploy:
      resources:
        limits:
          memory: 300M
  # 12 jdk8rabbitmq:3.8.16
  jdk8rabbitmq:
    image: jdk8rabbitmq:3.8.16
    container_name: jdk8rabbitmq
    build:
      context: /home/Software/RabbitMQ
      dockerfile: dfjdk8rabbitmq
    ports:
      - "5672:5672"
    deploy:
      resources:
        limits:
          memory: 300M
  # 13 os8redis:6.2
  os8redis:
    image: os8redis:6.2
    container_name: os8redis
    build:
      context: /home/Software/Redis
      dockerfile: dfos8redis
    ports:
      - "6379:6379"
    deploy:
      resources:
        limits:
          memory: 300M
  # 14 jdk8sonar:8.9
  jdk8sonar:
    image: jdk8sonar:8.9
    container_name: jdk8sonar
    build:
      context: /home/Software/Sonar
      dockerfile: dfjdk8sonar
    ports:
      - "9000:9000"
    deploy:
      resources:
        limits:
          memory: 300M
  # 15 os8tengine:2.3
  os8tengine:
    image: os8tengine:2.3
    container_name: os8tengine
    build:
      context: /home/Software/Tengine
      dockerfile: dfos8tengine
    ports:
      - "8888:80"
    deploy:
      resources:
        limits:
          memory: 300M
networks:
  default:
    external:
      name: envsy

docker-compose up -d

ssh-keygen -t rsa -b 2048 -f /etc/ssh/ssh_host_rsa_key

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

docker history centos8:8

docker rmi centos8:8

```







### 相关问题追查解决备注







### DONE