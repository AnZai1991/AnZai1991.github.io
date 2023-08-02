---
title: Zabbix实战
date: 2020-01-20 20:35:13
tags: [Zabbix,运维]
---

## 环境介绍
操作系统：CentOS 8
数据库版本：MySQL 8.0.17-3
Web环境：Apache 2.4.37
PHP版本：php 7.2.11
zabbix版本：zabbix 4.0

---

**名词解释**：
lnmp环境：Linux、Nginx、MySQL或MariaDB、PHP或Perl或Python
lamp环境：Linux、Apache、MySQL或MariaDB、PHP或Perl或Python
<!--more-->
**tips**:查看web服务是哪种
```bash
curl -I 127.0.0.1 #看server版本
```

## 步骤一、安装操作系统环境开启ftp服务
首先搭建好虚拟机，安装CentOS 8并更新
1、安装vsftpd
```bash
yum install -y vsftpd #安装vsftpd
systemctl start vsftpd.service #开启ftp服务
chkconfig vsftpd on #加入开机启动
```
2、更改ftp配置
```bash
vi /etc/vsftpd/vsftp.conf
anonymous_enable=NO #禁用匿名用户
```
3、
```bash
systemctl stop firewalld.service #关闭防火墙
systemctl disable firewalld.service #禁止防火墙自启
systemctl restart vsftpd.service #重启ftp服务
vi /etc/selinux/config #关闭SELinux
#将SELINUX=enforcing改为SELINUX=disabled
#必须！否则查看不到所有ftp上面的目录
```
最后重启完毕！

---

## 步骤二、部署MySQL数据库
下载MySQL社区版本：https://dev.mysql.com/downloads/
MySQL Community Server 社区版本是开源免费的，不提供技术支持
MySQL Enterprise Edition 企业版本，需付费，可试用30天
MySQL Cluster 集群版，开源免费，可将几个MySQL Server封装成一个Server
MySQL Cluster CGE 高级集群版，需付费

将安装文件上传到服务器后，安装MySQL
```bash
rpm -ivh mysql80-community-release-el8-1.noarch #安装软件包
ls -l /etc/yum.repos.d/mysql-community* #确认下是否是两个yum repo源
yum install mysql-server #安装mysql服务端
#yum install -y mysql-community-server 安装mysql服务端，没有前两步骤会报错没有可用包
systemctl start mysqld.service #开启mysql
systemctl status mysqld.service #查看mysql状态
```

---

## 步骤三、部署Web环境Nginx
在CentOS下，yum源不提供nginx的安装，可以通过切换yum源的方法获取安装，也可以直接下载安装包。
在nginx官方网站下载安装包http://nginx.org/en/download.html
下载安装包后使用ftp上传到服务器。解压缩安装包并源码编译安装
```bash
tar -zxvf nginx-1.17.7.tar.gz
cd nginx-1.17.7
ls
./configure
```
此时会报错./configure: error: C compiler cc is not found意思是说没有找到C编译器，需要安装gcc
#### 1、安装gcc
```bash
yum install -y gcc gcc-c++
```
再次运行
```bash
./configure
```
报错./configure: error: the HTTP rewrite module requires the PCRE library.意思是需要依赖pcre库
#### 2、安装pcre库
```bash
yum install -y pcre pcre-devel
```
再次运行
```bash
./configure
```
报错./configure: error: the HTTP gzip module requires the zlib library.意思是需要依赖zlib库
#### 3、安装zlib库
```bash
yum install -y zlib zlib-devel
```
再次运行
```bash
./configure
```
这次没有报错。输入make命令
```bash
make
```
此时提示make命令未找到，后面提示是否安装软件包‘make’以提供命令，输入y回车，后面在输入y回车
再次输入make命令
```bash
make
```
没有报错，再输入make install命令
```bash
make install
```
没有报错，查看/usr/local中是否有nginx文件夹，有的话表示nginx安装完成
#### 4、配置nginx
```bash
cd /usr/local/nginx
ls
```
会看到四个文件夹
conf：nginx配置文件所在的目录。
html：默认所带的页面所在的html
logs：日志所在的目录
sbin：nginx执行文件所在的目录

测试nginx配置文件是否正常
```bash
/usr/local/nginx/sbin/nginx -t
```
提示nginx命令未找到，是否安装nginx软件包提供命令'nginx'，选y回车
再次运行
```bash
/usr/local/nginx/sbin/nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
在sbin目录下运行nginx
```bash
nginx
```
查看下主机IP地址，此时可以打开浏览器，输入IP地址，就可以看到
![图片](/assets/img/article_5/Nginx.png "Nginx")

---

## 步骤四、安装php（以下仅供参考）
从官方网站下载PHP，上传到服务器并源码编译安装
```bash
tar -zxvf php-7.4.1.tar.gz
ls
cd php-7.4.1
./configure \
--prefix=/usr/local/php-7.4.1 \
--enable-gd \
--with-jpeg \
--with-freetype \
--enable-bcmath \
--enable-ctype \
--enable-xmlreader \
--enable-xmlwriter \
--enable-session \
--enable-sockets \
--enable-mbstring \
--with-gettext \
--with-mysqli=mysqlnd \

--with-config-file-path=/usr/local/pho-7.4.1/etc \
--with-bz2 \
--with-curl \
--enable-ftp \
--disable-ipv6 \
--with-iconv-dir=/usr/local \
--enable-calendar \
--with-libxml-dir=/usr/local \
--with-zlib \
--with-pdo-mysql=mysqlnd \
--with-mysql=mysqlnd \
--enable-dom \
--enable-xml \
--enable-fpm \
--with-libdir=lib64 \

make
make install
```

---

## 步骤五、安装zabbix
分别介绍两种方式，一种是直接利用zabbix官方的release版（推荐），可以免去上面的部署LNMP的步骤。另一种是源码编译安装，过程较复杂后续整理。
### 方法一、官方release版
此方法不需要前面的安装步骤，会打包安装所需的所有环境。
首先保证设备操作系统可以联网，访问zabbix的软件仓库网站，网址为http://repo.zabbix.com.
镜像站点目录non-supported是系统不支持的包目录，zabbix是安装包目录。
#### 1、安装zabbix4.0
以下以zabbix4.0版本为例
```bash
rpm -ivh http://repo.zabbix.com/zabbix/4.0/rhel/8/x86_64/zabbix-release-4.0-2.el8.noarch.rpm
```
#### 2、安装zabbix-server
因为某防火墙的原因，在做此操作时候，老是失败。所幸发现清华大学的镜像源网站有，所以先要做的是更新zabbix.repo源文件。
**更改下载源**
以下是原始文件内容：
```bash
vi /etc/yum.repos.d/zabbix.repo
[zabbix]
name=Zabbix Official Repository - $basearch
baseurl=http://repo.zabbix.com/zabbix/4.0/rhel/8/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX-A14FE591

[zabbix-non-supported]
name=Zabbix Official Repository non-supported - $basearch
baseurl=http://repo.zabbix.com/non-supported/rhel/8/$basearch/
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX
gpgcheck=1
```
更改后的文件：
```bash 
[zabbix]
name=Zabbix Official Repository - $basearch
#baseurl=http://repo.zabbix.com/zabbix/4.0/rhel/8/$basearch/
baseurl=https://mirrors.tuna.tsinghua.edu.cn/zabbix/zabbix/4.0/rhel/8/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX-A14FE591

[zabbix-non-supported]
name=Zabbix Official Repository non-supported - $basearch
#baseurl=http://repo.zabbix.com/non-supported/rhel/8/$basearch/
baseurl=https://mirrors.tuna.tsinghua.edu.cn/zabbix/non-supported/rhel/8/$basearch/
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-ZABBIX
gpgcheck=1
```
**安装**
```bash
yum install -y zabbix-server-mysql zabbix-web-mysql zabbix-agent zabbix-get
#在zabbix-server服务器上安装zabbix-agent，是为了通过agent方式监控
```
等待安装过程走完，可以通过rpm -qa查询所需要的MySQL、Apache、PHP是否都安装好
修改MySQL配置文件
```bash
vi  /etc/my.cnf
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks symbolic-links=0
**character-set-server=utf8** #设置字符集为utf-8
**innodb_file_per_table=1** #让InnoDB的每个表文件单独存储
max_connections=1000 #设置mysql最大连接数为1000，默认151

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```
启动服务并设置开机自启动
```bash
systemctl start mariadb #启动
systemctl enable mariadb #加入自启动
```
注：MySQL和MariaDB的区别：
LAMP架构盛极一时，这离不开MySQL的免费与易用，但是在Oracle收购了Sun之后，很多公司开始担忧MySQL的开源前景，而最近Oracle进一步闭源的举措更是让人难以安心，众多互联网公司纷纷开始寻求MySQL的替代方案。
不得不提的是Apple的远见，在Oracle收购Sun之初就宣布迁移到PostgreSQL。但PostgreSQL的设计初衷就不同于MySQL，并不是使用MySQL的大部分互联网公司合适的解决方案。除了Apple，Google、Facebook、Twitter也大量使用了MySQL，纷纷发布了自己的MySQL分支/补丁集，并为不少公司所采用。同时，MariaDB、Percona等MySQL分支也渐渐步入大众的视野。
MySQL之父Widenius先生离开了Sun之后，觉得依靠Sun/Oracle来发展MySQL，实在很不靠谱，于是决定另开分支，这个分支的名字叫做MariaDB。
MariaDB跟MySQL在绝大多数方面是兼容的，对于开发者来说，几乎感觉不到任何不同。目前MariaDB是发展最快的MySQL分支版本，新版本发布速度已经超过了Oracle官方的MySQL版本。
MariaDB 是一个采用Aria存储引擎的MySQL分支版本，是由原来 MySQL 的作者Michael Widenius创办的公司所开发的免费开源的数据库服务器。
这个项目的很多的代码都改编于 MySQL 6.0，例如 “pool of threads”功能提供解决多数据连接问题。MariaDB 5.1.41 RC可以到这里下载，32位和64位已编译Linux版本，还包括源代码包。MariaDB基于GPL 2.0发布。
所以对于大部分的MySQL用户来说，从现在主流的MySQL转到MariaDB应该是没有什么难度。

查看服务是否启动成功
```bash
ps -ef|grep mysql #查看进程
netstat -nlput|grep 3306 #查看端口
```
创建zabbix数据库
首先设置MySQL的root用户密码，然后创建zabbix数据库，设置访问策略
```bash
mysqladmin -uroot password admin #设置root用户密码为admin
mysql -uroot -padmin #登录MySQL
mysql> create database zabbix character set utf8; #创建名称为zabbix的数据库，并设置其字符集为utf-8
mysql> grant all privileges onzabbix.* to zabbix@'localhost' identified by 'zabbix';
mysql> grant all privileges onzabbix.* to zabbix@'127.0.0.1' identified by 'zabbix';
#设置zabbix数据库的所有权限，允许用户zabbix的IP地址为127.0.0.1和localhost访问，并将zabbix账号的密码设置为zabbix
mysql> flush privileges; #刷新权限，使其立即生效
\q #退出MySQL或者quit
```
导入zabbix库的数据文件
```bash
cd /usr/share/doc/zabbix-server-mysql #进入对应版本目录
gunzip create.sql.gz #将SQL文件解压缩
mysql -uzabbix -pzabbix -h 127.0.0.1 #已zabbix用户登录
mysql> use zabbix #切换到zabbix数据库
mysql> source /usr/share/doc/zabbix-server-mysql/create.sql; #导入sql文件
#create.sql是zabbix源码包中的3个sql文件的合集，分别为schame.sql、images.sql、data.sql，其中schema.sql是表结构；image.sql是图片相关的数据；data.sql是模板等相关数据。
#如果以源码方式安装zabbix-server则需要将这3个文件全部导入。如果是以源码方式安装zabbix-porxy的，则只能导入schames.sql。
cd /usr/share/doc/zabbix-proxy-mysql
gunzip schema.sql.gz
mysql> use zabbix-proxy
mysql> source /usr/share/doc/zabbix-proxy-mysql/schema.sql
```
**配置**
```bash
egrep -v "(^#|^$)" /etc/zabbix/zabbix_server.conf
LogFile=/var/log/zabbix/zabbix_server.log
LogFileSize=0
PidFile=/var/run/zabbix/zabbix_server.pid
SocketDir=/var/run/zabbix
DBHost=127.0.0.1
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
DBSocker=/var/lib/mysql/mysql.sock
DBPort=3306
StartPollers=100
StartIPMIPollers=10
StartPollersUnreachable=10
StartTrappers=10
StartPingers=10
StartDiscoverers=10
SNMPTrapperFile=/var/log/snmptt/snmptt.log
MaxHousekeeperDelete=500
CacheSize=256M
HistoryCacheSize=128M
TrendCacheSize=128M
#HistoryTextCacheSize=128M #这个参数不要照抄，没有的参数加上去会导致zabbix-server起不来
ValueCacheSize=2048M
Timeout=30
TrapperTimeout=300
UnreachablePeriod=45
UnavailableDelay=60
UnreachableDelay=15
AlertScriptsPath=/etc/zabbix/alertscripts
ExternalScripts=/etc/zabbix/externalscripts
FpingLocation=/usr/sbin/fping
LogSlowQueries=10000
StartProxyPollers=50
ProxyConfigFrequency=3600
```
**tips**：zabbix_server.conf配置文件详解： https://www.cnblogs.com/maxtgood/p/6143953.html 
告警和扩展脚本路径可能默认不存在，使用命令创建目录：
```bash
mkdir -p /etc/zabbix/alertscripts /etc/zabbix/externalscripts
```
**开启**
```bash
systemctl start zabbix-server
systemctl start httpd
ps -ef|grep zabbix #查看进程 看不到进程说明没起来
tail -f /var/log/zabbix/zabbix_server.log #查看日志 看不到日志说明没起来，有问题就来查看这个日志
systemctl enable zabbix-server #开机启动
systemctl enable httpd
```
防火墙和SElinux我都关掉了。
php.ini配置文件的设置
```bash
vim /etc/php.ini
date.timezone = Asia/Shanghai
max_execution_time = 300
post_max_size = 16M
max_input_time = 300
memory_limeit = 128M #如果web页面提示内存不够使用，请调整此值
mbstring.func_overload = 0 #zabbix2.2版本请设置为1，以后版本设置为0
```
```bash
cat -n file|grep "内容"
#鉴于php.ini配置文件内容过多，用此语句查找参数所在的行数，再进行修改。
```
**重启设备，php.ini更改才生效。**
如果配置web时有提示任何参数不满足安装或配置要求，修改后重启httpd
```bash
systemctl restart httpd
```
#### 常见问题
1、date timezone一直提示有问题，在保证防火墙和SElinux都关闭的情况下，先确保zabbix-server进程是否启动，同时查看日志是否正常，如果不正常，先从zabbix_server.conf下手，如果服务正常，最后只可能是php.ini里有问题，然后重启httpd服务。

### 方法二、源码编译安装
#### 1、安装依赖软件包
yum -y install gcc gcc-c++ autoconf httpd php mariadb-server php-mysql httpd-manual mod_ssl mod_perl mod_auth_mysql php-gd php-xml php-mbstring php-ldap php-pear php-xmlrpc php-bcmath mysql-connector-odbc mysql-devel libdbi-dbd-mysql net-snmp-devel curl-devel unixODBC-devel OpenIPMI-devel java-devel pcre-devel libxml2-devel openldap-devel libevent-devel openssl-devel libssh2-devel
#### 2、（未完待续）

---

## 步骤六、使用zabbix
### 配置zabbix-web
首次使用，需要配置zabbix-web
打开浏览器，输入http://IP地址/zabbix
安装完生成的官方配置文件会生成在/etc/zabbix/web/zabbix.conf.php
![图片](/assets/img/article_5/zabbix1.png "zabbix")
![图片](/assets/img/article_5/zabbix2.png "zabbix")
**（此处缺少两张图片后续补充）**
进入登录界面，默认账户是Admin，密码是zabbix
![图片](/assets/img/article_5/zabbix3.png "zabbix")
支持多语言，默认是英文的，更改成中文
![图片](/assets/img/article_5/zabbix6.png "zabbix")
禁用掉Guests组
![图片](/assets/img/article_5/zabbix7.png "zabbix")
### zabbix-Agent的安装
给服务端安装agent
```bash
yum installl -y zabbix zabbix-agent #前提是已经下载好zabbix的官方yum源，可以替换成清华大学的源，参考zabiix-server的安装
```
zabbix4.0官方各种系统版本的Agent：https://www.zabbix.com/cn/download_agents#tab:40LTS
配置zabbix_agentd.conf
```bash
egrep -v "(^#|^$)" /etc/zabbix/zabbix_agentd.conf
PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
Server=127.0.0.1,203.152.200.115 #被动模式，zabbix-server的IP地址
Server=127.0.0.1,203.152.200.115 #主动模式，zabbix-server的IP地址（建议）
Hostname=Zabbix server #本机的Hostname,使用主动模式则必须配置
Include=/etc/zabbix/zabbix_agentd.d/
UnsafeUserParameters=1 #启用特殊字符，用于自定义监控
```
配置完成后，启动zabbix-Agent并加入自启动
```bash
systemctl enable zabbix-agent
systemctl start zabbix-agent
```
至此就可以在web页面上看到服务端的监控信息了！
### 使用SNMP监控配置（以SUSE为例）
### 安装SNMP
在被监控端（SUSE 11）上安装snmp，从光盘中找到所需要的五个安装文件，其中最后两个要一起装，因为互相依赖。安装顺序为先snmp-libs再libsensors3、libsnmp15，最后两个一起。
![图片](/assets/img/article_5/snmp1.png "SNMP")
其他系统一般的安装方式为：
```bash
yum install -y net-snmp
```
### 配置SNMP
配置SNMP，先备份配置文件
```bash
cp /etc/snmpd/snmpd.conf /etc/snmpd/snmpd.conf.bak
vim /etc/snmpd/snmpd.conf
```
![图片](/assets/img/article_5/snmp2.png "SNMP")
```bash
# Please see /usr/share/doc/packages/net-snmp/EXAMPLE.conf for a
# more complete example and snmpd.conf(5).
#
# Writing is disabled by default for security reasons.  If you'd like
# to enable it uncomment the rwcommunity line and change the community
# name to something nominally secure (keeping in mind that this is
# transmitted in clear text).
# don't use ' < > in strings for syslocation or syscontact
# Note that if you define the following here you won't be able to change
# them with snmpset
syslocation Server Room
syscontact Sysadmin (root@localhost)
# These really aren't meant for production use.  They include all MIBS
# and can use considerable resources.  See snmpd.conf(5) for information
# on setting up groups and limiting MIBS.
rocommunity public 127.0.0.1
# rwcommunity mysecret 127.0.0.1
rocommunity public 203.xxx.xxx.xxx
# -----------------------------------------------------------------------------
###############################################################################
# disk checks
#
# The agent can check the amount of available disk space, and make
# sure it is above a set limit.  
# disk PATH [MIN=DEFDISKMINIMUMSPACE]
#
# PATH:  mount path to the disk in question.
# MIN:   Disks with space below this value will have the Mib's errorFlag set.
#        Default value = DEFDISKMINIMUMSPACE.
# Check the / partition and make sure it contains at least 10 megs.
disk / 10000 #设置小于10G发送snmp报文
disk /boot 5% #设置小于5%发送snmp报文
disk /rem 
# % snmpwalk -v 1 -c public localhost .1.3.6.1.4.1.2021.9
# enterprises.ucdavis.diskTable.dskEntry.diskIndex.1 = 0
# enterprises.ucdavis.diskTable.dskEntry.diskPath.1 = "/" Hex: 2F 
# enterprises.ucdavis.diskTable.dskEntry.diskDevice.1 = "/dev/dsk/c201d6s0"
# enterprises.ucdavis.diskTable.dskEntry.diskMinimum.1 = 10000
```
根据提示参考/usr/share/doc/packages/net-snmp/EXAMPLE.conf，对被控端的snmpd.conf进行配置，比如disk checks，就将配置拷贝过来就可以了。又比如配置文件的参数View可用于配置snmp可被snmpwalk查看和浏览的OID范围。
```bash
service snmpd restart #重启snmp
chkconfig snmpd on #添加到开机启动项
service snmpd status #检查snmp状态
```
**tips**：各种系统更改snmp配置参考https://blog.csdn.net/carechere/article/details/51491744
### 测试SNMP
使用snmpwalk获取SNMP数据进行测试
安装snmpwalk
```bash
yum install -y net-snmp-utils #使用snmpwalk需要安装
```
先测试本机地址，若不通检查snmp服务状态，再测试被监控端，只要通了就OK
```bash
#SNMPv2测试
snmpwalk -v 2c -c public 127.0.0.1
snmpwalk -v 2c -c public 127.0.0.1 SNMPv2-MIB::sysUpTime.0
#SNMPv3测试
Snmpwalk -v 3 -u snmp -a SHA -A SHA@PWD -x AES@PWD -l authPriv 172.18.3.4 SNMPv2-MIB::sysUpTime.0
```
测试服务端没有问题，就测试下客户端
```bash
snmpwalk -v 1 -c public 203.xxx.xxx.xxx .1.3.6.1.4.1.2021.9
UCD-SNMP-MIB::dskIndex.1 = INTEGER: 1
UCD-SNMP-MIB::dskPath.1 = STRING: /home
UCD-SNMP-MIB::dskDevice.1 = STRING: /dev/sda4
UCD-SNMP-MIB::dskMinimum.1 = INTEGER: 10000
UCD-SNMP-MIB::dskMinPercent.1 = INTEGER: -1
UCD-SNMP-MIB::dskTotal.1 = INTEGER: 51605464
UCD-SNMP-MIB::dskAvail.1 = INTEGER: 20201744
UCD-SNMP-MIB::dskUsed.1 = INTEGER: 28782316
UCD-SNMP-MIB::dskPercent.1 = INTEGER: 59
UCD-SNMP-MIB::dskPercentNode.1 = INTEGER: 1
UCD-SNMP-MIB::dskErrorFlag.1 = INTEGER: noError(0)
UCD-SNMP-MIB::dskErrorMsg.1 = STRING: 
snmpwalk -v 1 -c public 203.xxx.xxx.xxx .1.3.6.1.4.1.2021.9.1.9
UCD-SNMP-MIB::dskPercent.1 = INTEGER: 59
```
那么监控/home挂载点使用率就返回成功了，最后要取的OID就是.1.3.6.1.4.1.2021.9.1.9.1（注意最后要再加个1）
参考https://blog.csdn.net/u014403008/article/details/53780059/
### 添加到web页面
通过web管理页面添加设备到服务端
![图片](/assets/img/article_5/zhuji1.png "添加主机")
![图片](/assets/img/article_5/zhuji2.png "添加主机")
![图片](/assets/img/article_5/addsnmp.png "添加snmp项")
**tips**：使用snmp监控参考：https://blog.csdn.net/L835311324/article/details/82986703

#### 常见问题
1、配置完成登录后发现Zabbix server is running一直是NO。通过telnet本机的10051查看，要是不通，就看一下进程，进程也没有就查一下zabbix的日志，zabbix的日志都没有，重启zabbix_server看提示，根据提示去查看系统的日志为什么服务没有起来。服务起来之后zabbix日志就正常显示，再根据zabbix日志排查错误。
```bash
/etc/init.d/zabbix_server restart #重启zabbix_server
/etc/init.d/zabbix_server status #查看zabbix_server状态
```
我遇到的情况如下：
经系统日志发现unknown parameter [] in config file [/usr/local/zabbix/etc/zab...]根据提示解决了这个报错。
zabbix服务器起来后，log就可以看到了，但是页面依然显示是NO，通过查看日志，看到报错[Z3001]connection to database 'zabbix' failed: [1040] Too many connections
解决方法参考修改最大链接数：https://blog.51cto.com/net881004/2089198
```bash
vim /etc/my.cnf #修改配置
max_connections=1000 #添加此行
```
重启mariadb服务，验证最大连接数，没生效就执行以下（可能不需要，下次部署记得确认）
```bash
sql>show variables like 'max_connections';
vim /usr/lib/systemd/system/mariadb.service
#在[Service]新加这两行：
LimitNOFILE=10000
LimitNPROC=10000
```
修改了最大连接数重启后正常。
2、一开始有怀疑是/usr/share/zabbix/conf/zabbix.conf.php文件没有的缘故，这个目录下只有一个zabbix.conf.php.example我简单改了一下，然后又把zabbix-server配置完成时候官方生成的/etc/zabbix/zabbix.conf.php拷贝过来一份，最后发现这个东西似乎没啥作用。
3、解决Too many processes on zabbix server
方法一：修改减少StartPollers参数和StartProxyPollers参数
StartPollers初始化时，启动子进程数量，数量越多，则服务端吞吐能力越强，对系统资源消耗越大
StartProxyPollers启用多少子进程与代理端通信，若代理端较多可考虑加大此数值
方法二：修改触发器的上限
![图片](/assets/img/article_5/chufaqi.png "修改触发器上限")
4、图形界面中文乱码根据下图解决
![图片](/assets/img/article_5/中文乱码.png "中文乱码")
找下Windows系统上的字体，如微软雅黑，在C:\Windows\Fonts\微软雅黑下，拷贝一个到服务器上
```bash
mv DejaVuSans.ttf DejaVuSans.ttf.bak #备份
mv msyh.ttf DejaVuSans.ttf #替换玩刷新一下就好了
```