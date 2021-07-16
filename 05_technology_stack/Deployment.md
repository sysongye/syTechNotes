# <font color=#69D600>Project Deployment</font>

[TOC]

## 基于 `Docker` 容器部署

### Requirements:

​	`Docker`	沙箱容器

​	`Nginx`	Web服务器

​	`MySQL`	关系型数据库

​	`Redis`	内存中的数据结构存储系统



#### Docker 安装

详见 [Docker Install.md](https://github.com/sysongye/syTechNotes/blob/master/03_linux_install/Docker%20Install.md)



#### Nginx 拉取和配置

```perl
# 拉取 Nginx 最新版本
docker pull Nginx

# -v /home/docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
# 小坑，docker挂载文件报错 not a directory，先在一个test容器中复制一份配置文件
docker run --name test -d nginx  
docker cp test:/etc/nginx/nginx.conf /home/docker/nginx/nginx.conf
docker stop test
docker rm test

# 如果不知道配置文件的存放目录，可以进去容器查看一下
docker exec -it test /bin/bash

# 创建并启动容器，设置端口、开机启动，建立目录映射，多目录挂载
docker run -d \
--name nginx --restart=always \
-p 80:80 -p 443:443 \
-e "TZ=Asia/Shanghai" \
-v /home/docker/nginx/nginx.conf:/etc/nginx/nginx.conf \
-v /home/docker/nginx/conf.d:/etc/nginx/conf.d \
-v /home/docker/nginx/logs:/var/log/nginx \
-v /home/docker/nginx/cert:/etc/nginx/cert \
-v /home/docker/nginx/html:/usr/share/nginx/html \
nginx


```



#### MySQL 拉取和配置

```perl
# 查找镜像
docker search mysql

# 拉取 MySQL5.7 版本，大众版本
docker pull mysql:5.7

# 创建并启动容器，设置端口、root 用户密码、开机启动，不建/建立 目录映射，多目录挂载
docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=songye --restart=always -d mysql:5.7
docker run -p 3306:3306 --name mysql \
-v /home/docker/mysql/conf:/etc/mysql \
-v /home/docker/mysql/logs:/var/log/mysql \
-v /home/docker/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=songye \
--restart=always -d mysql:5.7

# 进入容器
docker exec -it mysql bash

# 进入数据库
mysql -u root -p

# source 导入sql
source /etc/mysql/baseDB.sql
```



#### Redis 拉取和配置

```perl
# 拉取 Redis 最新版本
docker pull redis

# 创建并启动容器，设置端口、开机启动
docker run -itd --name redis --restart=always -p 6379:6379 redis
```



#### 上传项目jar

```perl
https://el-admin.vip/# 项目jar
/home/docker/eladmin/eladmin-system-2.6.jar

# 创建 Dockerfile
cat > Dockerfile
FROM java:8
ARG JAR_FILE=./*.jar
COPY ${JAR_FILE} app.jar
ENV TZ=Asia/Shanghai
ENTRYPOINT ["java","-jar","/app.jar"]

# 构建项目镜像
docker build -t eladmin .

# 创建并启动容器，将容器中的 /home/eladmin/ 挂载到宿主机的 /home/docker/eladmin/data/，目录并且设置数据库地址与密码等环境变量参数
docker run -d \
--name eladmin --restart=always \
-p 8000:8000 \
-e "TZ=Asia/Shanghai" \
-e DB_HOST=172.17.0.1 \
-e DB_PWD=mysql_pwd \
-e REDIS_HOST=172.17.0.1 \
-v /home/docker/eladmin/data/:/home/eladmin/ \
eladmin

# 显示已有容器信息
docker ps
docker container ls
docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}"
docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Command}}\t{{.RunningFor}}\t{{.Status}}"
docker ps -a --no-trunc --format "table {{.Names}}\t{{.Command}}\t{{.Ports}}"
docker ps -a --no-trunc --format "table {{.Names}}\t{{.Mounts}}"




########## 华丽丽分割线 ##########
--------------------------------------------------------------------


# 在用 Vue 构建大型应用时推荐使用 NPM 安装[1]。NPM 能很好地和诸如 webpack 或 Browserify 模块打包器配合使用。同时 Vue 也提供配套工具来开发单文件组件。
# 最新稳定版
yum install nodejs

npm install vue

# 如果已经全局安装了旧版本的 vue-cli，需要先通过 npm uninstall vue-cli -g 或 yarn global remove vue-cli 卸载它
npm uninstall vue-cli -g
# OR
yarn global add @vue/cli

npm install -g @vue/cli
# OR
yarn global add @vue/cli


npm run build:prod

ls -l home/eladmin

https://el-admin.vip/
```













### DONE