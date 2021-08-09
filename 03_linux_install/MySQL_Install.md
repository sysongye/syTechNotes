# <font color=#69D600>MySQL Install</font>

[TOC]

#### Version: mysql-5.7.32

平台：CentOS Linux release 8.2.2004 (Core)

> Note: 华为镜像下载速度快 https://mirrors.huaweicloud.com/home





创建用户和用户组

```
groupadd mysql
useradd -g mysql mysql

# 检查是否已安装 libaio，没有需要安装
yum install libaio
```





### 下载文件

```perl
# 递归创建目录，用于保存下载的文件，目录建议放在数据盘
mkdir -p /root/software/mysql
mkdir -p /var/lib/mysql
cd /root/software/mysql
# 下载文件 系统有 wget 命令的
wget https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz
# 下载文件 系统有 curl 命令的
curl -O https://repo.huaweicloud.com/mysql/Downloads/MySQL-5.7/mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz

# 解压文件
tar xzvf mysql-5.7.32-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.32-linux-glibc2.12-x86_64 /usr/local/mysql

```





### 开始安装

```perl
# 切换目录所属用户和用户组
chown -R mysql:mysql /usr/local/mysql

# mysql_install_db 已弃用，使用 mysqld --initialize
/usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/
[WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize

# 初始化 如出现 error while loading shared libraries: libaio.so.1 需要安装 yum install libaio
/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data/ --defaults-file=/etc/my.cnf

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



修改密码和权限

```
SET PASSWORD = PASSWORD('songye');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
use mysql;
update user set host = '%' where user = 'root';
FLUSH PRIVILEGES;
```







### 相关问题追查解决备注





### DONE



