# <font color=#69D600>Oracle Database</font>

[TOC]

### 官网相关

​	Website: https://www.oracle.com/index.html

​	Docs: http://shiro.apache.org/documentation.html

​	Download: https://www.oracle.com/database/technologies/oracle-database-software-downloads.html#19c



### Tips





角色	dba_roles

系统权限	dba_sys_privs

对象权限	dba_tab_privs



DBA: 拥有全部特权，是系统最高权限，只有DBA才可以创建数据库结构。

RESOURCE:拥有Resource权限的用户只可以创建实体，不可以创建数据库结构。

CONNECT:拥有Connect权限的用户只可以登录Oracle，不可以创建实体，不可以创建数据库结构。

对于普通用户：授予connect, resource权限。
对于DBA管理用户：授予connect，resource, dba权限。



Oracle默认登录密码
用户名 / 密码                      登录身份                              说明


sys/change_on_install       SYSDBA 或 SYSOPER        不能以 NORMAL 登录，可作为默认的系统管理员

system/manager               SYSDBA 或 NORMAL         不能以 SYSOPER 登录，可作为默认的系统管理员


sysman/oem_temp             sysman                            为 oms 的用户名


scott/tiger                        NORMAL                            普通用户

aqadm /aqadm                SYSDBA 或 NORMAL        高级队列管理员

Dbsnmp/dbsnmp           SYSDBA 或 NORMAL           复制管理员





### DONE