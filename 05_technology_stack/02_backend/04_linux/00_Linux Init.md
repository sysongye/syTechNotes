# <font color=#69D600>

# Linux Initialization</font>

[TOC]

### 平台 CentOS 8

最初环境：最小化安装	Minimal Install



#### 区域/语言


系统安装时语言选的 English，/usr/lib/locale 也是 en_US，locale 命令查看却是 zh_CN.UTF-8，可能是因为时区 **Time & Date** 选了 Shanghai

```
// root 用户在 /etc/profile.d/ 添加 locale.sh
# cat > /etc/profile.d/locale.sh
. /etc/locale.conf
```



1、家庭用途版本有：Linux Mint、Ubuntu、OpenSUSE、Fedora、PC-BSD。
2、商业用途版本有：Debian、RHEL、CentOS。
3、挑战用途版本有：Gentoo、LFS。
4、理想用途版本有：FreeBSD、OpenBSD、Solaris、OpenSolaris。

Linux的基本思想有两点：第一，一切都是文件；第二，每个文件都有确定的用途。

systemctl set-default multi-user.target

#### 用户管理


最小化安装系统后，只配置 root 用户情况下



```
// 查看 1、3 列，组名、组 id
awk -F':' '{ print $1,$3}' /etc/group
// 查看 1-3 列，组名、密码、组 id
cut -d: -f1-3 /etc/group
// 查看 组名
compgen -g

cat /etc/group

grep dev /etc/group | awk -F: '{print $1}'
```

##### 添加用户组

```
groupadd dev
groupadd pro
groupadd test
```



##### 查看用户

```
// 查看 1、3 列，用户名、组 id
awk -F':' '{ print $1,$3}' /etc/passwd
// 查看 1-3 列，用户名、密码、组 id
cut -d: -f1-3 /etc/passwd
// 查看 用户名
compgen -u

cat /etc/passwd | grep 1000
```



##### 添加用户

```
useradd -d /home/dev -g dev -G test dev
useradd -d /home/pro -g pro pro
useradd -d /home/songye -g dev -G pro,test songye

// 查看各组情况
egrep -e "dev|pro|songye" /etc/passwd
egrep -e "dev|pro|test" /etc/group

// 用户所属组如果不正常重新修改
usermod -g dev -G pro,test songye
usermod -g dev -G test dev
usermod -g pro pro
```



##### 添加用户密码
```
passwd dev
dev123
passwd pro
pro123
passwd songye
songye
```



### 平台 Ubuntu 20 LTS

Ubuntu 默认禁用 root 用户

添加密码
```
sudo passwd root
```

启用 root

```
cat <<EOF >> /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf
greeter-show-manual-login=true
EOF

cat /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf
```



命令行启动

```
vi /etc/default/grub
```

修改为 "quiet splash 3"

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash 3"
```

run 'update-grub' afterwards to update /boot/grub/grub.cfg.

```
update-grub
```



区域/语言

```
// 直接修改 locale 文件，替换 zh_CN
vi /etc/default/locale
:%s/zh_CN/en_US/g
```



安装 SSH

```
apt install openssh-server
apt install openssh-client
```

修改配置文件

```
vim /etc/ssh/sshd_config

// 去除注释
#LoginGraceTime 2m
#PermitRootLogin prohibit-password
#StrictModes yes

LoginGraceTime 2m
PermitRootLogin yes
StrictModes yes
```

重启 SSH 服务

```
service ssh restart
```



### DONE