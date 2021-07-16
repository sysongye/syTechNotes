# <font color=#69D600>Python Install</font>

[TOC]

#### Version: Python-3.9.6.tgz

平台：CentOS Linux release 8.2.2004 (Core)

> Note: 华为镜像下载速度快 https://mirrors.huaweicloud.com/home





### 下载文件

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /root/software/python
cd /root/software/python
# 下载文件 系统有 wget 命令的
wget https://repo.huaweicloud.com/python/3.9.6/Python-3.9.6.tgz
# 下载文件 系统有 curl 命令的
curl -O https://repo.huaweicloud.com/python/3.9.6/Python-3.9.6.tgz

# 解压文件
tar xzvf Python-3.9.6.tgz

# 安装一些依赖开发包
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel libffi-devel
```





### 开始安装

```perl
cd /root/software/python/Python-3.9.6
./configure
make
make test
make install
```



```
python3 -m pip install --upgrade pip setuptools wheel

python3 -m pip install supervisor
```







### 相关问题追查解决备注





### DONE



