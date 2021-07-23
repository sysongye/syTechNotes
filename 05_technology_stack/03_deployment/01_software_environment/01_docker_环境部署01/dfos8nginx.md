# <font color=#69D600>os8nginx:1.20 Dockerfile</font>

[TOC]

#### Version: os8nginx:1.20

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 以 centos8:8 镜像作为基础



### 下载文件

> Note: 文件较小，也可以在 Dockerfile 下

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /home/Software/Nginx
cd /home/Software/Nginx
# 下载文件 系统有 wget 命令的
wget https://repo.huaweicloud.com/nginx/nginx-1.20.0.tar.gz
# 下载文件 系统有 curl 命令的
curl -O https://repo.huaweicloud.com/nginx/nginx-1.20.0.tar.gz


# Nginx 配置参数 --with-http_geoip_module 需要 GeoIP 库，基础镜像没有，宿主机下载传到镜像再安装
# GeoIP 根据来访者的 IP， 定位该 IP 所在经纬度、国家/地区、省市、和街道等位置信息。
yum install --downloadonly --downloaddir=/home/Software/Nginx GeoIP GeoIP-devel
=====================
Installing:
GeoIP-1.6.12-7.el8.x86_64.rpm
GeoIP-devel-1.6.12-7.el8.x86_64.rpm
Installing dependencies:
GeoIP-GeoLite-data-2018.06-5.el8.noarch.rpm
=====================

# yum install GeoIP GeoIP-devel                         # CentOS
# apt-get install php5-geoip php5-dev libgeoip-dev      # Ubuntu
```



### Dockerfile

​		基础镜像 `os8nginx:1.20` 的 Dockerfile 和 supervisord.conf

​		Filename: dfos8nginx

​		合理利用 &&，减少 RUN 生成中间层镜像。

​		使用 ADD 可直接解压好压缩文件

​		注意 COPY 和 ADD 适用场景，使用 history 分析中间层大小，合理利用可以使镜像更小

> Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```
cd /home/Software/Nginx

# new
# Dockerfile
cat > dfos8nginx
FROM centos8:8
MAINTAINER songye
LABEL desc="base on centos8:8 image"

ADD ./nginx-1.20.0.tar.gz /usr/local/src
COPY ./GeoIP*.rpm /home/Software
RUN useradd -M -s /sbin/nologin nginx \
    && rpm -ih /home/Software/GeoIP*.rpm && rm -f /home/Software/GeoIP*.rpm \
    && yum install -y libxslt-devel gd gd-devel && yum clean all \
    && mkdir -p /usr/local/nginx/data \
    && cd /usr/local/src/nginx-1.20.0 \
    && ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-file-aio \
    --with-http_ssl_module --with-http_realip_module --with-http_addition_module --with-http_xslt_module \
    --with-http_image_filter_module --with-http_geoip_module --with-http_sub_module --with-http_dav_module \
    --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module \
    --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module \
    --with-http_degradation_module --with-http_stub_status_module \
    && make && make install

VOLUME ["/usr/local/nginx/data"]
ENV PATH=$PATH:/usr/local/nginx/sbin

EXPOSE 80
ENTRYPOINT ["nginx"]
CMD ["-g"]

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

docker build --force-rm -f dfos8nginx -t os8nginx:1.20 .

docker history os8nginx:1.20

docker rmi os8nginx:1.20

docker run -d -p 80:80 --name nginx os8nginx:1.20 -g "daemon off;"

docker run -d -p 80:80 -v nginx/usr/local/nginx/data --name nginx os8nginx:1.20 -g "daemon off;"

docker run -d -p 81:80 -v nginx/usr/local/nginx/data --name nginx2 os8nginx:1.20 -g "daemon off;"

docker run -p 81:80 --name nginx2 os8nginx:1.20 -g "daemon off;"

docker exec -it nginx /bin/bash
ls /usr/local/nginx/data


docker volume ls
docker logs -f f6b8ffd63e83
docker logs -tail 20 -f ab479c4a35e5
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





借鉴备份


~~~
# base image
FROM centos

# MAINTAINER
MAINTAINER songye

# put nginx-1.12.2.tar.gz into /usr/local/src and unpack nginx
ADD nginx-1.12.2.tar.gz /usr/local/src

# running required command
RUN yum install -y gcc gcc-c++ glibc make autoconf openssl openssl-devel 
RUN yum install -y libxslt-devel -y gd gd-devel GeoIP GeoIP-devel pcre pcre-devel
RUN useradd -M -s /sbin/nologin nginx

# mount a dir to container
VOLUME ["/data"]

# change dir to /usr/local/src/nginx-1.12.2
WORKDIR /usr/local/src/nginx-1.12.2

# execute command to compile nginx
RUN ./configure --user=nginx --group=nginx --prefix=/usr/local/nginx --with-file-aio  --with-http_ssl_module  --with-http_realip_module    --with-http_addition_module    --with-http_xslt_module   --with-http_image_filter_module    --with-http_geoip_module  --with-http_sub_module  --with-http_dav_module --with-http_flv_module    --with-http_mp4_module --with-http_gunzip_module  --with-http_gzip_static_module  --with-http_auth_request_module  --with-http_random_index_module   --with-http_secure_link_module   --with-http_degradation_module   --with-http_stub_status_module && make && make install

# setup PATH
ENV PATH /usr/local/nginx/sbin:$PATH

# EXPOSE
EXPOSE 80

# the command of entrypoint
ENTRYPOINT ["nginx"]

CMD ["-g"]
~~~







### 相关问题追查解决备注







### DONE