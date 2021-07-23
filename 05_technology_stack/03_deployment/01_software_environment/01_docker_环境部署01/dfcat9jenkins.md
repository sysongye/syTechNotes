# <font color=#69D600>cat9jenkins:2.289 Dockerfile</font>

[TOC]

#### Version: cat9jenkins:2.289

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 以 os8tomcat:9 镜像作为基础



### 下载文件

> Note: 文件较大

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /home/Software/Jenkins
cd /home/Software/Jenkins
# 下载文件 系统有 wget 命令的
wget https://repo.huaweicloud.com/jenkins/war-stable/2.289.2/jenkins.war
# 下载文件 系统有 curl 命令的
curl -O https://repo.huaweicloud.com/jenkins/war-stable/2.289.2/jenkins.war

```



### Dockerfile

​		基础服务 `cat9jenkins:2.289` 的 Dockerfile

​		Filename: dfcat9jenkins

​		合理利用 &&，减少 RUN 生成中间层镜像。

​		使用 ADD 可直接解压好压缩文件

​		注意 COPY 和 ADD 适用场景，使用 history 分析中间层大小，合理利用可以使镜像更小

> Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```
cd /home/Software/Jenkins

# new
# Dockerfile
cat > dfcat9jenkins
FROM os8tomcat:9
MAINTAINER songye
LABEL desc="base on os8tomcat:9 image"

COPY ./jenkins.war /usr/local/apache-tomcat-9.0.10/webapps/ROOT/
RUN cd /usr/local/apache-tomcat-9.0.10/webapps/ROOT/ \
 && rm -rf `ls | grep -v jenkins.war` \
 && unzip jenkins.war && rm -f jenkins.war \
 && cd .. && rm -rf `ls | grep -v ROOT` \
 && yum install -y git maven && yum clean all

EXPOSE 8090
ENTRYPOINT /usr/local/apache-tomcat-9.0.10/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.10/logs/catalina.out

```



### docker build

```
## Note: 
# 删除中间层容器，默认是 true，成功构建镜像后删除中间层容器，不成功不删除。测试需要分析中间层容器可以加 --rm=false
--rm	true	Remove intermediate containers after a successful build
# 强制删除中间层容器，默认官方没注明，也是 true，总是删除中间层容器，构建镜像不成功也删除
--force-rm		Always remove intermediate containers
# 不使用缓存，基于 FROM 相同镜像构建时，可能产生和使用相同的缓存，增加该参数则不使用缓存，另外生成新的缓存，即产生更多 REPOSITORY 为 <none> 的镜像，非特殊需求不推荐使用
--no-cache		Do not use cache when building the image

docker build --force-rm -f dfcat9jenkins -t cat9jenkins:2.289 .

docker history cat9jenkins:2.289

docker rmi cat9jenkins:2.289

docker run -itd --name jenkins cat9jenkins:2.289 /bin/bash

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