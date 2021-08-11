# <font color=#69D600>Oracle database Install</font>

[TOC]

Oracle官网下载：https://www.oracle.com/database/technologies/oracle-database-software-downloads.html

Doc: https://docs.oracle.com/en/database/oracle/oracle-database/19/index.html

#### Version: LINUX.X64_193000_db_home	oracle-database-ee-19c

平台：CentOS Linux release 8.2.2004 (Core)

文件：LINUX.X64_193000_db_home.zip

> https://yum.oracle.com/repo/OracleLinux/OL7/latest/x86_64/getPackage/oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm
>
> http://www.rpmfind.net/linux/rpm2html/search.php





yum install -y ksh libaio-devel sysstat libnsl

rpm -ivh compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm

rpm -ivh compat-libcap1-1.10-7.el7.x86_64.rpm

rpm -ivh oracle-database-preinstall-19c-1.0-1.el7.x86_64.rpm

rpm -ivh oracle-database-ee-19c-1.0-1.x86_64.rpm

/etc/init.d/oracledb_ORCLCDB-19c configure -datafileDestination=/home/oracle/oradata



/opt/oracle/product/19c/dbhome_1/bin/dbca -silent -createDatabase \
  -templateName General_Purpose.dbc \
  -gdbname cdb1 -sid cdb1 -responseFile NO_VALUE \
  -characterSet AL32UTF8 \
  -sysPassword oracle \
  -systemPassword oracle \
  -createAsContainerDatabase true \
  -numberOfPDBs 1 \
  -pdbName pdb1 \
  -pdbAdminPassword oracle \
  -databaseType MULTIPURPOSE \
  -automaticMemoryManagement false \
  -totalMemory 2000 \
  -storageType FS \
  -datafileDestination "/home/oracle/oradata" \
  -redoLogFileSize 50 \
  -emConfiguration NONE \
  -ignorePreReqs



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



