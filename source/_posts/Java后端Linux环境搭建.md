---
title: Java后端Linux环境搭建
tags:
  - Linux
categories:
  - [Linux]
mathjax: false
date: 2020-06-11 00:13:53
---



参考[文档](https://github.com/hansonwang99/JavaCollection)，基于CentOS 7.7.1908

---

## 安装须知

###  安装文件

准备以下软件的Linux安装文件

+ Maven
+ Tomcat
+ Zookeeper
+ Git
+ JDK
+ Kafka
+ MySQL
+ Nginx
+ Node
+ Python
+ Redis

### 安装的基本步骤

1. 解压安装文件至/usr/local/下创建的文件夹下
2. 修改软件的配置文件（可选）
3. 配置软件路径到/etc/profile文件中，刷新环境变量`source /ect/profile`
4. 查看软件安装是否成功，查看软件的版本

### 各软件版本查看方式整理

1. Git：`git --version`

2. Java：`java -version`
3. Node：`node -v`、`npm version`、`npx -v`
4. Python：`python -V`、`python3 -V`（注意`V`大写）
5. Maven：`mvn -v`
6. Nginx：`nginx -v`

## Git

### 通过yum包管理器安装

```shell
yum install git
```

通过`git --version`查看是否安装成功

安装的版本是`git version 1.8.3.1`

## JDK安装

安装Oracle JDK



## node环境安装

![node]( http://img.whl123456.top/image/image-20200611002658715.png)

![image-20200611002658715]( http://img.whl123456.top/image/image-20200611002658715.png)

## Maven

### 遇到的问题

在配置Maven环境变量中失误在`$MAVEN_HOME`和`/bin:$path`之间打了个空格

![image-20200612155538407]( http://img.whl123456.top/image/image-20200612155538407.png)

在退出vim，执行`source /etc/profile`报错

```shell
/usr/libexec/grepconf.sh: line 5: grep: command not found
-bash: export: `/bin:/usr/local/sbin:/usr/sbin:/usr/local/node/node-v12.16.3-linux-x64/bin:/usr/local/maven/apache-maven-3.6.3:/root/bin:/usr/local/java/jdk1.8.0_161/bin': not a valid identifier
```

此后，导致如`ls`、`vim`等命令不能使用，也不能使用其他命令修改profile，可先执行

```shell
export PATH=/bin:/usr/bin:$PATH
```

临时更改环境变量在找出配置错误的环境变量并修改

## MySQL（多次安装失败）

前提

卸载系统自带的Mariadb

```shell
rpm -qa|grep mariadb
```

如果有Mariadb包，使用如下方式全部卸载

```shell
yum -y remove 包名
```

下载或者使用准备好的安装包

```
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```

解压并重命名文件夹

```shell
tar -zxvf /root/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz -C /usr/local/
```

```shell
mv mysql-5.7.24-linux-glibc2.12-x86_64 mysql
```

创建MySQL用户和用户组

```shell
groupadd mysql
```

```shell
useradd -g mysql mysql
```

创建data目录

```shell
mkdir /usr/local/mysql/data
```

修改MySQL目录的归属用户

```shell
chown -R mysql:mysql /usr/local/mysql
```

配置MySQL的配置文件

```shell
vim /etc/my.cnf
```

加入内容

```shell
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
socket=/var/lib/mysql/mysql.sock
[mysqld]
skip-name-resolve
#设置3306端⼝
port = 3306
socket=/var/lib/mysql/mysql.sock
# 设置mysql的安装⽬录
basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放⽬录
datadir=/usr/local/mysql/data
# 允许最⼤连接数
max_connections=200
# 服务端使⽤的字符集默认为8⽐特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使⽤的默认存储引擎
default-storage-engine=INNODB
lower_case_table_names=1
max_allowed_packet=16M
```

```shell
mkdir /var/lib/mysql
chmod 777 /var/lib/mysql
```

安装MySQL

```shell
cd /usr/local/mysql
```

```shell
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```

此时遇到一个问题

```shell
./mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
```

安装

```shell
yum install libaio
```

在重新

```shell
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
```

记下`root@localhost：`后的密码

![image-20200612162005461]( http://img.whl123456.top/image/image-20200612162005461.png)

启动MySQL

```
service mysqld start
```

报错无法启动MySQL，原因是MariaDB

对于以下报错，个人尝试多种方式都未能解决，故不使用MySQL转向MariaDB

```shell
Failed to restart mysqld.service: Unit not found.
```

问题2

使用的阿里云ECS，再一次修改MySQL配置文件后，无法使用Workbench远程连接，但可以使用VNC进行连接

阿里云ECS登录实例失败，原因：拒绝连接

问题未找到，最后无奈通过重新初始化磁盘才可以连接

## MariaDB

MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

MariaDB由MySQL的创始人Michael Widenius（英语：Michael Widenius）主导开发，他早前曾以10亿美元的价格，将自己创建的公司MySQL AB卖给了SUN，此后，随着SUN被甲骨文收购，MySQL的所有权也落入Oracle的手中。

> MariaDB数据库安装[参考](https://www.jianshu.com/p/85ad52c88399)

使用yum命令安装mariaDB

```shell
yum -y install mariadb mariadb-server
```

启动mariaDB

```shell
systemctl start mariadb
```

设置开机启动

```shell
systemctl enable mariadb
```

进行简单相关配置

```shell
mysql_secure_installation
```

![image-20200612205521591]( http://img.whl123456.top/image/image-20200612205521591.png)

配置mariaDB字符集

```shell
vi /etc/my.cnf
```

添加

```
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
```

![image-20200612205634824]( http://img.whl123456.top/image/image-20200612205634824.png)

```shell
vi /etc/my.cnf.d/client.cnf
```

添加

```
default-character-set=utf8
```

![image-20200612205809998]( http://img.whl123456.top/image/image-20200612205809998.png)

```
vi /etc/my.cnf.d/mysql-clients.cnf
```

添加

```
default-character-set=utf8
```

![image-20200612205933709]( http://img.whl123456.top/image/image-20200612205933709.png)

重启mariadb

```shell
systemctl restart mariadb
```

登录mariaDB，查看MariaDB字符集

```sql
mysql -uroot -p

show variables like "%character%";

show variables like "%collation%";
```

远程连接

在登录情况下输入以下SQL语句

```sql
use mysql;

select host,user,password from user;
```

![image-20200612210519878]( http://img.whl123456.top/image/image-20200612210519878.png)

```SQL
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '为远程连接设置的密码'；
```

> 参考[文章](https://www.cnblogs.com/lanuage/p/8179589.html)

以Navicat为例连接远程MariaDB

![image-20200612213409377]( http://img.whl123456.top/image/image-20200612213409377.png)

## Nginx

```
yum install nginx

nginx -v
```

查看安装目录

```
rpm -ql nginx
```

```
/etc/logrotate.d/nginx
/etc/nginx/fastcgi.conf
/etc/nginx/fastcgi.conf.default
/etc/nginx/fastcgi_params
/etc/nginx/fastcgi_params.default
/etc/nginx/koi-utf
/etc/nginx/koi-win
/etc/nginx/mime.types
/etc/nginx/mime.types.default
/etc/nginx/nginx.conf
/etc/nginx/nginx.conf.default
/etc/nginx/scgi_params
/etc/nginx/scgi_params.default
/etc/nginx/uwsgi_params
/etc/nginx/uwsgi_params.default
/etc/nginx/win-utf
/usr/bin/nginx-upgrade
/usr/lib/systemd/system/nginx.service
/usr/lib64/nginx/modules
/usr/sbin/nginx
/usr/share/doc/nginx-1.16.1
/usr/share/doc/nginx-1.16.1/CHANGES
/usr/share/doc/nginx-1.16.1/README
/usr/share/doc/nginx-1.16.1/README.dynamic
/usr/share/doc/nginx-1.16.1/UPGRADE-NOTES-1.6-to-1.10
/usr/share/licenses/nginx-1.16.1
/usr/share/licenses/nginx-1.16.1/LICENSE
/usr/share/man/man3/nginx.3pm.gz
/usr/share/man/man8/nginx-upgrade.8.gz
/usr/share/man/man8/nginx.8.gz
/usr/share/nginx/html/404.html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/en-US
/usr/share/nginx/html/icons
/usr/share/nginx/html/icons/poweredby.png
/usr/share/nginx/html/img
/usr/share/nginx/html/index.html
/usr/share/nginx/html/nginx-logo.png
/usr/share/nginx/html/poweredby.png
/usr/share/vim/vimfiles/ftdetect/nginx.vim
/usr/share/vim/vimfiles/ftplugin/nginx.vim
/usr/share/vim/vimfiles/indent/nginx.vim
/usr/share/vim/vimfiles/syntax/nginx.vim
/var/lib/nginx
/var/lib/nginx/tmp
/var/log/nginx
```

启动nginx

```
/usr/sbin/nginx
```

设置配置文件

```
vi /etc/nginx/nginx.conf
```

停止服务

```
/usr/sbin/nginx -s stop
```

重新加载

```
/usr/sbin/nginx -s reload
```

