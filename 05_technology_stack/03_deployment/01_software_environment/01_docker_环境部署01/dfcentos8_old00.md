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

yum install -y
# centos
passwd openssh-server openssh-clients initscripts net-tool python-setuptools supervisor
# jdk
deltarpm rpm make wget tar unzip gcc gcc-c++
# mysql
libaio numactl autoconf wget tar gcc gcc-c++
# RabbitMQ
gcc glibc-devel make ncurses-devel openssl-devel xmlto perl wget xz lsof unixODBC-devel tar && \
    rpm --rebuilddb
# redis
deltarpm rpm make tar gcc gcc-c++ epel-release
# Tengine
deltarpm rpm make wget tar unzip gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel

langpacks-en glibc-all-langpacks \
    gcc gcc-c++ make passwd initscripts net-tools rpm wget tar unzip\
    openssh-server openssh-clients python3-setuptools \
    glibc-devel pcre pcre-devel zlib zlib-devel openssl openssl-devel \
    bzip2-devel ncurses-devel sqlite-devel readline-devel tk-devel libffi-devel
```



### Dockerfile

​		基础镜像 centos8:8 的 Dockerfile

​		dfcentos8

> 合理利用 &&，减少 RUN 生成中间层镜像

> Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```
cd /root/software

# Dockerfile
cat > dkfile/dfcentos8
FROM centos
MAINTAINER songye
LABEL desc="base on centos:latest image"
RUN mkdir -p /root/software/python
COPY ./python/Python-3.9.6.tgz /root/software/python
RUN yum install -y langpacks-en glibc-all-langpacks \
    gcc gcc-c++ make passwd initscripts net-tools rpm wget tar unzip\
    openssh-server openssh-clients python3-setuptools \
    glibc-devel pcre pcre-devel zlib zlib-devel openssl openssl-devel \
    bzip2-devel ncurses-devel sqlite-devel readline-devel tk-devel libffi-devel \
    && yum clean all \
    && echo 'root:dksongye' | chpasswd \
    && /usr/bin/ssh-keygen -f id_rsa

WORKDIR /root/software/python
# RUN ./configure && make && make test && make install \
RUN tar zxf Python-3.9.6.tgz && rm -f Python-3.9.6.tgz && cd Python-3.9.6 \
	&& ./configure && make && make install \
	&& rm -rf ./*
RUN python3 -m pip install --upgrade pip setuptools wheel \
    && python3 -m pip install supervisor

EXPOSE 22
CMD /usr/sbin/sshd -D

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

docker build --force-rm -f dkfile/dfcentos8 -t centos8:8 .

docker build --force-rm -f dkfile/dfcentos8 -t centos88:8.2 .

docker build --no-cache -f dkfile/test -t test ./dkfile

docker build --rm -f dkfile/test -t test ./dkfile

--
docker build --force-rm -f dkfile/test -t test ./dkfile


docker run -it test
docker start 4732f1948e52
docker attach 4732f1948e52
docker run -it 2d8be27f4eb1
docker run -it 269b3d245a3d


docker build -f dkfile/test -t test ./dkfile

docker build --force-rm=true --no-cache -f dkfile/test -t test ./dkfile

docker images -a
docker ps -qa
docker rmi test

ls /var/lib/docker/overlay2/


cat > dkfile/test
FROM centos
MAINTAINER songye
RUN mkdir -p /root/software/python
COPY Python-3.9.6.tgz /root/software/python
RUN chmod 777 /root/software/python/Python-3.9.6.tgz && rm -rf /root/software/python/Python-3.9.6.tgz
RUN yum install -y  gcc make && yum clean all
RUN rm -rf /root/software/python/Python-3.9.6/*


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
docker pull centos:7

cat > dfcentos7
FROM centos:7
MAINTAINER songye
# RUN yum -y update
RUN yum install -y passwd openssh-server openssh-clients initscripts net-tools
RUN yum install -y python3-setuptools
RUN easy_install supervisor
RUN echo 'root:dksongye' | chpasswd
RUN /usr/sbin/sshd-keygen

EXPOSE 22
CMD /usr/sbin/sshd -D

docker build -f dfcentos7 -t centos7 .
docker run -it --name centos7 300e315adb2f

docker rm -f $(docker ps -qa)
docker rmi $(docker images -f "dangling=true" -q)


### supervisor 通过脚本进行多应用的启动

##### 使用yum install python-setuptools -y来安装supervisor

> 默认的yum安装不了 supervisor

RUN yum install python-setuptools -y
RUN easy_install supervisor
~~~







### Options

docker build options

| Name, shorthand           | Default | Description                                                  |
| ------------------------- | ------- | ------------------------------------------------------------ |
| `--add-host`              |         | Add a custom host-to-IP mapping (host:ip)                    |
| `--build-arg`             |         | Set build-time variables                                     |
| `--cache-from`            |         | Images to consider as cache sources                          |
| `--cgroup-parent`         |         | Optional parent cgroup for the container                     |
| `--compress`              |         | Compress the build context using gzip                        |
| `--cpu-period`            |         | Limit the CPU CFS (Completely Fair Scheduler) period         |
| `--cpu-quota`             |         | Limit the CPU CFS (Completely Fair Scheduler) quota          |
| `--cpu-shares` , `-c`     |         | CPU shares (relative weight)                                 |
| `--cpuset-cpus`           |         | CPUs in which to allow execution (0-3, 0,1)                  |
| `--cpuset-mems`           |         | MEMs in which to allow execution (0-3, 0,1)                  |
| `--disable-content-trust` | `true`  | Skip image verification                                      |
| `--file` , `-f`           |         | Name of the Dockerfile (Default is 'PATH/Dockerfile')        |
| `--force-rm`              |         | Always remove intermediate containers                        |
| `--iidfile`               |         | Write the image ID to the file                               |
| `--isolation`             |         | Container isolation technology                               |
| `--label`                 |         | Set metadata for an image                                    |
| `--memory` , `-m`         |         | Memory limit                                                 |
| `--memory-swap`           |         | Swap limit equal to memory plus swap: '-1' to enable unlimited swap |
| `--network`               |         | [**API 1.25+**](https://docs.docker.com/engine/api/v1.25/) Set the networking mode for the RUN instructions during build |
| `--no-cache`              |         | Do not use cache when building the image                     |
| `--output` , `-o`         |         | [**API 1.40+**](https://docs.docker.com/engine/api/v1.40/) Output destination (format: type=local,dest=path) |
| `--platform`              |         | [**API 1.38+**](https://docs.docker.com/engine/api/v1.38/) Set platform if server is multi-platform capable |
| `--progress`              | `auto`  | Set type of progress output (auto, plain, tty). Use plain to show container output |
| `--pull`                  |         | Always attempt to pull a newer version of the image          |
| `--quiet` , `-q`          |         | Suppress the build output and print image ID on success      |
| `--rm`                    | `true`  | Remove intermediate containers after a successful build      |
| `--secret`                |         | [**API 1.39+**](https://docs.docker.com/engine/api/v1.39/) Secret file to expose to the build (only if BuildKit enabled): id=mysecret,src=/local/secret |
| `--security-opt`          |         | Security options                                             |
| `--shm-size`              |         | Size of /dev/shm                                             |
| `--squash`                |         | [**experimental (daemon)**](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)[**API 1.25+**](https://docs.docker.com/engine/api/v1.25/) Squash newly built layers into a single new layer |
| `--ssh`                   |         | [**API 1.39+**](https://docs.docker.com/engine/api/v1.39/) SSH agent socket or keys to expose to the build (only if BuildKit enabled) (format: default\|<id>[=<socket>\|<key>[,<key>]]) |
| `--stream`                |         | Stream attaches to server to negotiate build context         |
| `--tag` , `-t`            |         | Name and optionally a tag in the 'name:tag' format           |
| `--target`                |         | Set the target build stage to build.                         |
| `--ulimit`                |         | Ulimit options                                               |







### 相关问题追查解决备注

```
1、所用 Docker 的 centos8 镜像，没有 gcc make 等多个基础命令，需要自己装
2、CentOS8 yum 源不再提供 python-setuptools，新版本有 python2-setuptools、python3-setuptools，但是也都没有 easy_install 命令，应该是命令已过时
3、CentOS8 /usr/sbin/sshd-keygen 不存在，存在 /usr/bin/ssh-keygen
4、为了安装 supervisor，安装 python3-setuptools 无 easy_install 命令，安装了新版 python3，里面的 setuptools 同样没有 easy_install 命令，所以用 python3 的 pip 方式安装 supervisor
```







### DONE