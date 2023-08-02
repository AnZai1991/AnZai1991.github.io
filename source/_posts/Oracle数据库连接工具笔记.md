---
title: Oracle数据库连接工具笔记
date: 2022-04-17 15:13:59
tags: [数据库,运维]
---

# 环境介绍
操作系统：Windows或Linux
软件版本：Oracle 11g,PL/SQL,OracleInstant Client,SQLPlus

# 本地连接
## SQLPlus
sqlplus的使用方法
运行cmd
输入sqlplus /nolog
回车
输入conn 账号/密码@oracle服务器IP/实例名
回车
提示已连接，即成功。
<!--more-->
# 远程连接
## PLSQL Developer + Oracle Client
1、安装Oracle Client(客户端) 
2、选择管理员安装
3、剩下的就是一路下一步就OK，安装完成后在cmd窗口执行，sqlplus如果出现如下输入用户名，就表示安装成功
4、安装PLSQL
5、安装完成后现在开始配置连接，安装Oracle Client后已经自动将安装目录添加到环境变量（系统变量）中
6、配置PLSQL点击软件先不登陆，然后等弹出软件界面后，在菜单中找到工具–首选项
添加Oracle Client主目录和OCI库
7、配置完PLSQL后，配置Oracle数据库信息。进入Oracle Client的安装目录找到network下的 admin下的tnsnames.ora文件
8、按照如下格式添加：
```bash
DB203_152_100_134(自定义) =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 203.152.100.134（Oracle所在服务器地址）)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = orcl(实例名，默认为orcl))
      (SID = zxin)
    )
  )
```
9、本地需要配置的已经完成，接下来检查服务器上的相关文件，首先查看Oracle服务器安装目录下的tnsnames.ora文件和listener.ora文件
如果配置文件中的host后面的值和本机名不一样那么启动监听会报错或者无法连接，Windows也是一样要和本机名一样，如果是Linux系统如果修改了本机名字还要保证/etc/hosts下有添加记录
10、最后查看端口 1521 是否开启，Windows防火墙中开放端口，Linux可以通过命令

```bash
firewall-cmd –zone=public –add-port=1521/tcp –permanent
```
来开启，并重启防火墙。使用命令查看1521端口信息
```bash
netstat -n -a
```
## PLSQL Developer + Oracle Instant Client
1、下载
这是[Oracle Instant Client的官方下载链接](http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html)，有很多种版本可供下载。
安装exe版本和免安装zip版本区别是有两点：
**免安装版本需要配置环境变量**
（在“环境变量”的“系统变量”中增加，Path添加D:\instantclient_11_2，TNS_ADMIN = D:\instantclient_11_2\network\admin，若出现乱码需要添加NLS_LANG = SIMPLIFIED CHINESE_CHINA.ZHS16GBK）
**免安装解压后在目录下创建network目录，在network目录下创建admin目录，然后创建tnsnames.ora文件**
但要注意第三方工具如：PL/SQL Developer和Toad的版本，32位的要对应32位的OracleInstant Client，不要因为系统是64位的就下载64位的，这个要注意。
2、配置
PLSQL首选项配置只需要选择使用Instant Client的oci库=D:\Oracle\Instant Client\bin\oci.dll
3、修改tnsnames.ora文件
在D:\Oracle\Instant Client\network\admin 找到tnsnames.ora文件，增加自己的数据库别名配置。示例如下

```bash
DB203_152_121_114 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 203.152.121.114)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVICE_NAME = orcl)
      (SID = zxin)
    )
  )
```
注意格式要排列好
主要改 = 前面的别名，Host为IP地址, SERVICE_NAME为数据库服务器的实例名。
4、上面的任何一种客户端配置好后，都可以安装Toad 或者PL/SQL Developer 工具，不需要再额外进行任何设置，即可使用。

### 问题汇总
1、cmd命令行找不到sqlplus命令
答：需要在环境变量-系统变量中添加sqlplus命令的所在路径（移动到最前面）
Path=D:\Oracle\Instant Client\bin
2、使用的安装版本Oracle Instant Client，且sqlplus能正常远程连接，但是plsql报错ORA-12154: TNS: 无法解析指定的连接标识符
答：需要在环境变量-系统变量中添加tnsnames.ora的路径
TNS_ADMIN = D:\instantclient_11_2\netword\admin
3、plsql连接成功后，有中文乱码
答：出现乱码需要在环境变量-系统变量中添加
NLS_LANG = SIMPLIFIED CHINESE_CHINA.ZHS16GBK

# 参考
<http://blog.csdn.net/magicboylinw/article/details/7025885>
<https://www.cnblogs.com/gongguan/p/12685801.html>