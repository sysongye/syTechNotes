# <font color=#69D600>os8mysql:5.7.32 Dockerfile</font>

[TOC]

#### Version: os8mysql:5.7.32

平台：CentOS Linux release 8.2.2004 (Core)

Docker：Docker version 20.10.7

> Note: 华为镜像下载速度快 https://mirrors.huaweicloud.com/home



### 下载文件

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /home/Software/MySQL
cd /home/Software/MySQL
# 下载文件 系统有 wget 命令的
wget https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz
# 下载文件 系统有 curl 命令的
curl -O https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz

```



### Dockerfile

> ​		基础服务 `os8mysql:5.7.32` 的 Dockerfile 和 supervisord.conf
>
> ​		Filename: dfos8mysql
>
> ​		合理利用 &&，减少 RUN 生成中间层镜像。
>
> ​		使用 ADD 可直接解压好压缩文件
>
> ​		注意 COPY 和 ADD 适用场景，使用 history 分析中间层大小，合理利用可以使镜像更小
>
> > Note: 软件的安装方法可能有很多种，不同系统不同版本命令也可能不一样，导致出现无法预料的问题，所以最好先测试 Dockerfile，排查解决好问题，生成正常的 REPOSITORY 而非 <none> ，这样可行的 Dockerfile 再放入 docker compose 里面，方便多台相同配置机器上进行快速部署。

```
cd /home/Software/MySQL

# new
# Dockerfile
cat > dfos8mysql
FROM centos8:8
MAINTAINER songye
LABEL desc="base on centos8:8 image"

COPY ./mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz /usr/local/
COPY ./my.cnf /etc/my.cnf
COPY ./supervisord.conf /etc/supervisord.conf

RUN yum install -y libaio numactl autoconf && yum clean all
RUN groupadd mysql && useradd -g mysql mysql \
 && mkdir -p /var/lib/mysql/ && chown -R mysql:mysql /var/lib/mysql \
 && cd /usr/local/ && tar zxf mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz \
 && rm -f mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz \
 && mv /usr/local/mysql-5.7.32-linux-glibc2.12-x86_64 /usr/local/mysql \
 && chown -R mysql:mysql /usr/local/mysql \
 && /usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf --initialize --user=mysql --basedir=/usr/local/mysql \
 --datadir=/usr/local/mysql/data/ --lower-case-table-names=1 \
 && chown -R mysql:mysql /usr/local/mysql/data \
 && cp /usr/local/mysql/support-files/mysql.server /etc/rc.d/init.d/mysqld \
 && chkconfig mysqld on \
 && echo "export PATH=\$PATH:/usr/local/mysql/bin" >> /etc/profile.d/usrenvpath.sh

ENV PATH=$PATH:/usr/local/mysql/bin
EXPOSE 3306
CMD ["/usr/local/bin/supervisord"]


# supervisord.conf
cat > supervisord.conf
[supervisord]
nodaemon=true
[program:mysqld]
command=systemctl start mysqld


################
# 创建用户和用户组
groupadd mysql
useradd -g mysql mysql

# 检查是否已安装 libaio，没有需要安装
yum install libaio

# 切换目录所属用户和用户组
chown -R mysql:mysql /usr/local/mysql

# mysql_install_db 已弃用，使用 mysqld --initialize
/usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/
[WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize

# 初始化 如出现 error while loading shared libraries: libaio.so.1 需要安装 yum install libaio
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data/ --defaults-file=/etc/my.cnf --lower-case-table-names=1

[Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
[Warning] InnoDB: New log files created, LSN=45790
[Warning] InnoDB: Creating foreign key constraint system tables.
# 初次安装提示
[Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 9a65f11c-dfdc-11eb-bf2c-fa163ee8dc2f.
[Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
[Warning] CA certificate ca.pem is self signed.
# 随机生成临时的 root 初始密码，登录修改密码才能操作数据库
[Note] A temporary password is generated for root@localhost: 7WD4levi3u;;

# 切换目录所属用户和用户组
chown -R mysql:mysql /usr/local/mysql/data/
chown -R mysql:mysql /var/lib/mysql

# 配置服务
cp /usr/local/mysql/support-files/mysql.server /etc/rc.d/init.d/mysqld
# 启动服务
systemctl start mysqld
# 开机启动服务
chkconfig mysqld on

# 添加环境变量
# 临时生效的环境变量
export PATH=$PATH:/usr/local/mysql/bin
# 重启生效的环境变量
echo "export PATH=\$PATH:/usr/local/mysql/bin" >> /etc/profile.d/usrenvpath.sh


mysqld --verbose --help | grep -A 2 'Default options'
mysqld --verbose --help | grep -C 2 'Default options'
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

docker build --force-rm -f dfos8mysql -t os8mysql:5.7.32 .

docker history os8mysql:5.7.32

docker rmi os8mysql:5.7.32

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





修改密码和权限

```
SET PASSWORD = PASSWORD('DevEnv123$');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
use mysql;
update user set host = '%' where user = 'root';
FLUSH PRIVILEGES;
```







### 相关问题追查解决备注







### DONE