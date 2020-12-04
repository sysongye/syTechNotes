# <font color=#69D600>Linux Command</font>

[TOC]

### 通用

```perl
# 通用
ps -ef | grep thread_name
cat file | grep -i string
cat file | head -n 10
wc file

cat -n 文件路径 | tail -n +5 | head -n 6   // 显示 5 ～ 10 行的内容， 包括5 和10
zcat TW_LAB_WORKF_DIL_D_SZ_20190825_00.txt.Z  | tail -n +5000 | head -n 10

zcat TW_MRKT_USR_M_SZ_201907_00.txt.Z | awk -F '|' '{if(NF!=83) print NR,NF}' | head -5

cat test3.txt | awk -F ' ' '{a[$1]++}END{for(i in a){print i,a[i] | "sort -k 2nr"}}'

nohup /home/staff/qinl/perl201805/PRO_SZ.sh > /home/staff/qinl/perl201805/PRO_SZ.out&

DF

DU      -- ALL
DU -s   -- TOT
DU DIR  -- DIR ALL
du -sh * | sort -h
lsof | grep deleted
killall -9 vim

compress -f TW_LAB_WORKF_DIL_D_SZ_20190819_000000.txt
uncompress TW_LAB_WORKF_DIL_D_SZ_20190819_000000.txt.Z

sed -i '1i########## 华丽丽分割线 ##########|ABC' test1.txt
sed -i 's/\\N//g' /data/GZ/tzgz_tw_corp_grid_m_bak_${vMonth}.txt -- 替换字符
iconv /data/GZ/tzgz_tw_corp_grid_m_bak_${vMonth}.txt -f UTF-8 -t ASCII -o /data/GZ/tzgz_tw_corp_grid_m_GZ_${vMonth}.txt
iconv -c --verbose  -f utf-8 -t gb2312 index_utf8.html -o index_gb2312.html

lunix version
uname -a
uname -r

less /proc/version  -- then q to quit
less /etc/issue     -- then q to quit
lsb_release -a


# 此文件为系统的每个用户设置永久环境信息,当用户第一次登录时,该文件被执行并从/etc/profile.d目录的配置文件中搜集shell的设置
/etc/profile

# 为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取
/etc/bashrc

# 每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件 仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件
~/.bash_profile																~/

# 该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取
~/.bashrc																	~/

# 当每次退出系统(退出bash shell)时,执行该文件
~/.bash_logout																~/

    . 
```



### rpm

```PERL
########## 华丽丽分割线 ##########
--------------------------------------------------------------------
## 平台：CentOS8

#############
##   yum   ##
### 已安装 ###

# 查询系统已安装的rpm包
rpm -qa
rpm -qa | grep xx

# 查询系统中一个已知的文件属于哪个rpm包

rpm -qf /path/file_name

# 查询已安装的软件包的相关文件的安装路径
rpm -ql xx

# 查询一个已安装软件包的信息
rpm -qi xx

# 查看已安装软件的配置文件
rpm -qc xx

# 查看已安装软件的文档的安装位置
rpm -qd xx

# 查看已安装软件所依赖的软件包及文件
rpm -qR xx

## 未安装 ##
# 查看系统未安装软件相关命令
rpm -qpi xx

# 查看软件包所包含的目录和文件
rpm -qpl xx

# 查看软件包的文档所在的位置
rpm -qpd xx

# 查看软件包的配置文件（若没有，则标准输出就为空）
rpm -qpc xx

# 查看软件包的依赖关系
rpm -qpR xx
```



### yum

```perl
########## 华丽丽分割线 ##########
--------------------------------------------------------------------
## 平台：CentOS8

#############
##   yum   ##
### 已安装 ###

# 使用yum查找软件包
yum search xx

# 列出所有可安装的软件包
yum list xx

# 列出所有可更新的软件包
yum list updates

# 列出所有已安装的软件包
yum list installed

# 列出所有已安装但不在 Yum Repository 内的软件包
yum list xx

# 使用yum获取软件包信息 、显示yum包的信息
yum info xx

# 列出所有已安装但不在 Yum Repository 内的软件包信息
yum info extras

# 列出软件包提供哪些文件
yum provides

# 更新具体的yum包
yum update xx

# 显示已启用的yum存储库的列表
yum repolist

# 清除yum缓存
yum clean all

# 找出哪个yum包提供了一个特定的文件（例如：/usr/bin/nc)）
yum whatprovides "*bin/nc"

# 卸载yum包装
yum remove xx

# 取出yum包装
yum downloader xx

# 重新安装一个yum包
yum reinstall xx
```





### DONE



