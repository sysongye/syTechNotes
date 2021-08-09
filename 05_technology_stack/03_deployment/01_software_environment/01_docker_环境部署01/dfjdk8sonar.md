# <font color=#69D600>jdk8sonar:8.9 Dockerfile</font>

[TOC]

#### Version: jdk8sonar:8.9

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 以 os8jdk8:291 镜像作为基础



### 下载文件

> Note: 文件较大

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /home/Software/Sonar
cd /home/Software/Sonar
# 下载文件 系统有 wget 命令的
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.1.44547.zip
wget https://github.com/xuhuisheng/sonar-l10n-zh/releases/download/sonar-l10n-zh-plugin-8.9/sonar-l10n-zh-plugin-8.9.jar

# 下载文件 系统有 curl 命令的
curl -O https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.1.44547.zip
curl -O https://github.com/xuhuisheng/sonar-l10n-zh/releases/download/sonar-l10n-zh-plugin-8.9/sonar-l10n-zh-plugin-8.9.jar
curl -O https://binaries.sonarsource.com/Distribution/sonar-java-plugin/sonar-java-plugin-7.2.0.26923.jar
curl -O https://binaries.sonarsource.com/Distribution/sonar-typescript-plugin/sonar-typescript-plugin-2.1.0.4359.jar

```



### Dockerfile

​		基础服务 `jdk8sonar:8.9` 的 Dockerfile 和 supervisord.conf

​		Filename: dfjdk8sonar

​		合理利用 &&，减少 RUN 生成中间层镜像。

​		使用 ADD 可直接解压好压缩文件

​		注意 COPY 和 ADD 适用场景，使用 history 分析中间层大小，合理利用可以使镜像更小

> Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```
cd /home/Software/Sonar

# new
# Dockerfile
cat > dfjdk8sonar
FROM os8jdk8:291
MAINTAINER songye
LABEL desc="base on os8jdk8:291 image"

COPY ./sonarqube-8.9.1.44547.zip /usr/local/
COPY ./supervisord.conf /etc/supervisord.conf
COPY ./sonar-typescript-plugin-2.1.0.4359.jar /usr/local/sonarqube-8.9.1.44547/extensions/plugins/
COPY ./sonar-java-plugin-7.2.0.26923.jar /usr/local/sonarqube-8.9.1.44547/extensions/plugins/

RUN cd /usr/local/ && unzip -o sonarqube-8.9.1.44547.zip \
# 创建 elksearch 用户组及 elksearch 用户（因为启动 elksearch 不能使用root用户）
 && groupadd elksearch && useradd elksearch -g elksearch -p DevEnv123$ \
 && touch /home/Software/CentOS/Python/supervisord.log \
 && chown -R elksearch:elksearch /home/Software/CentOS/Python/supervisord.log \
 && mkdir -p /usr/local/sonarqube-8.9.1.44547/extensions/downloads \
 && chown -R elksearch:elksearch /usr/local/sonarqube-8.9.1.44547/elasticsearch \
 && chmod -R 777 /usr/local/sonarqube-8.9.1.44547/ \
 && touch /home/Software/CentOS/Python/supervisord.log \
 && chown -R elksearch:elksearch /home/Software/CentOS/Python/supervisord.log

COPY sonar.properties  /usr/local/sonarqube-8.9.1.44547/conf/sonar.properties

# 在非 root 用户下运行
USER elksearch

EXPOSE 9200
EXPOSE 9300
CMD ["/usr/local/bin/supervisord"]


# 汉化包 sonar-l10n-zh-plugin-1.19.jar

# supervisord.conf
cat > supervisord.conf
[supervisord]
nodaemon=true
[program:elasticsearch]
command=/usr/local/sonarqube-8.9.1.44547/elasticsearch/bin/elasticsearch -d
[program:sonar]
command=/usr/local/sonarqube-8.9.1.44547/bin/linux-x86-64/sonar.sh start
user=elksearch

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

docker build --force-rm -f dfjdk8sonar -t jdk8sonar:8.9 .

docker history jdk8sonar:8.9

docker rmi jdk8sonar:8.9

docker run -it --name sonar jdk8sonar:8.9 /bin/bash

docker rm sonar

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