# <font color=#69D600>Docker Install</font>

[TOC]

官网安装教程：https://docs.docker.com/engine/install/centos/

#### Version: docker-ce latest

平台：CentOS8

> 说明：Docker Engine 
>
> Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从Apache2.0协议开源
>
> Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化
>
> 容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低
>
> 相关笔记：[Docker Command](../04_linux_command/Docker Command.md)

> docker-ce	Docker 社区版



### 开始安装

#### Prerequisites:

OS requirements

需要 Linux 内核版本高于 3.10

```
# uname -r
4.18.0-147.el8.x86_64
```



#### Uninstall old versions:

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```



#### Install using the repository:

Set up the repository 设置yum源

```
$ sudo yum install -y yum-utils

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

Failed to set locale, defaulting to C.UTF-8

系统安装时语言选的 English，/usr/lib/locale 也是 en_US，locale 命令查看却是 zh_CN.UTF-8，可能是因为时区 **Time & Date** 选了 Shanghai

```
# locale
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=zh_CN.UTF-8
LC_CTYPE="zh_CN.UTF-8"
LC_NUMERIC="zh_CN.UTF-8"
LC_TIME="zh_CN.UTF-8"
LC_COLLATE="zh_CN.UTF-8"
LC_MONETARY="zh_CN.UTF-8"
LC_MESSAGES="zh_CN.UTF-8"
LC_PAPER="zh_CN.UTF-8"
LC_NAME="zh_CN.UTF-8"
LC_ADDRESS="zh_CN.UTF-8"
LC_TELEPHONE="zh_CN.UTF-8"
LC_MEASUREMENT="zh_CN.UTF-8"
LC_IDENTIFICATION="zh_CN.UTF-8"
LC_ALL=            
```

临时解决方案：

export LC_ALL=en_US.UTF-8

export LANG=en_US.UTF-8

```
# export LC_ALL=en_US.UTF-8
# locale
LANG=zh_CN.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8
# export LANG=en_US.UTF-8
```

优选解决方案：

```
// 查看 locale.conf 文件
# cat /etc/locale.conf
// en_US 则正常，zh_CN 需要修改为 en_US
LANG="en_US.UTF-8"

// 加载配置文件即生效，仅当前登录有效，重启变无效。亦可用 . /etc/locale.conf 命令
# source /etc/locale.conf

// root 用户在 /etc/profile.d/ 添加 locale.sh，系统启动会自动加载
# cat > /etc/profile.d/locale.sh
. /etc/locale.conf
```



可选项：启用夜间或测试存储库，默认禁用，通过 --enable、--disable 切换

 ```perl
sudo yum-config-manager --enable docker-ce-nightly
sudo yum-config-manager --enable docker-ce-test
# 禁用
sudo yum-config-manager --disable docker-ce-nightly
 ```



#### Install Docker Engine:

1、Install the *latest version* of Docker Engine and containerd, or go to the next step to install a specific version:

安装最新版 Docker 引擎和容器，或下一步安装指定版本。

```
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

```
Error: 
 Problem 1: problem with installed package podman-1.4.2-5.module_el8.1.0+237+63e26edc.x86_64
 ......
 Problem 2: problem with installed package buildah-1.9.0-5.module_el8.1.0+237+63e26edc.x86_64
 ......
```

CentOS8 默认集成了 Podman、Buildah，类似 Docker 的虚拟技术，与 Docker 冲突，暂时先删了冲突的 podman、runc

不一定有冲突，有冲突再删

```
sudo yum remove podman
sudo yum remove runc
```

继续安装成功



2、To install a *specific version* of Docker Engine, list the available versions in the repo, then select and install:

查看现有版本，安装指定版本 Docker

```
sudo yum list docker-ce --showduplicates | sort -r
sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

启动Docker并设置开机启动

```
sudo systemctl start docker
sudo systemctl enable docker
```

默认仓库太慢 https://hub.docker.com/，修改为阿里云镜像库

```
cd /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://drg4ifzq.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



#### Uninstall Docker Engine:

卸载 Docker，默认 Images, containers, volumes, or customized 等配置文件不会自动删除，需要手动删

```
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```



### 检测

开启 daemon 守护进程

```
# sudo systemctl start docker

# docker version
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46d9d
 Built:             Wed Sep 16 17:02:36 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.13
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       4484c46d9d
  Built:            Wed Sep 16 17:01:11 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.3.7
  GitCommit:        8fba4e9a7d01810a393d5d25a3621dc101981175
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

docker run hello-world 第一次没有镜像自动从官网仓库拉取，再 run 一次

```
# sudo docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
......
```

停止 daemon 守护进程

```
# sudo systemctl stop docker
```



### 相关问题追查解决备注
CentOS8 默认集成 Podman、Buildah，考虑是否使用 Podman 替换 Docker



> Ubuntu install docker
>
> \# apt  install docker.io  # version 20.10.2-0ubuntu1~20.04.2
>
> apt install -y docker.io





### DONE



