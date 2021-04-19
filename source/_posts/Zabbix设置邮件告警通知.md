---
title: Zabbix设置邮件告警通知
date: 2021-04-19 21:06:42
tags: [操作系统,Linux,Windows,网络,运维]
---

## 环境介绍
操作系统：CentOS 8
zabbix版本：zabbix4.0
邮箱软件：mailx
email:163邮箱和沃邮箱

---

## 概念
<!--more-->
什么是MTA？什么是MUA？
通俗的说MUA就是用户使用的客户端，而MTA则是真正发送邮件的发送进程，MUA编写的邮件最终也交由MTA发送至目标端。
mailx即为负责查看、编写邮件和向MTA发送邮件的MUA。
sendmail和postfix即为负责邮件在网络上传输的MTA，将邮件从一个MTA传送至另一个MTA。
标注：使用zabbox服服务端本地邮箱账号发送邮件，需要安装两个软件达到报警邮件转发至指定邮箱。sendmail或postfix（两个软件各选择一个，但不能同时运行）和mailx邮件客户端。

0、安装sendmail或postfix （邮件传送代理MTA），本教程使用sendmail软件。（**如果直接使用外部邮箱发送邮件可以不需要配置sendmail或postfix直接下一步步**）
```bash
[root@ssticentos65 ~]# yum -y install sendmail                #在线yum安装sendmail
[root@ssticentos65 ~]# /etc/init.d/sendmail start            #启动sendmail服务
[root@ssticentos65 ~]# /etc/init.d/sendmail status          #查看sendmail启动情况
sendmail dead but subsys locked                                          #sendmail进程锁住，原因是postfix服务启动导致sendmail服务进程锁住，需要关闭postfix服务。
sm-client (pid  1759) is running...
[root@ssticentos65 ~]# /etc/init.d/postfix stop                #暂停postfix服务提示失败，原因是postfix进程正在使用，需要使用kill命令杀掉postfix进程
Shutting down postfix:                                     [FAILED]
[root@ssticentos65 ~]# /etc/init.d/postfix status                #查看postfix服务可以查看到进程号
master (pid  1647) is running...
[root@ssticentos65 ~]# kill -9 1647                                     #杀掉postfix服务进程号
[root@ssticentos65 ~]# /etc/init.d/postfix status                #查看postfix状态
master dead but pid file exists
[root@ssticentos65 ~]# chkconfig postfix off                      #设置postfix开机不启动
[root@ssticentos65 ~]# /etc/init.d/sendmail stop               #暂停sendmail服务
Shutting down sm-client:                                   [  OK  ]
Shutting down sendmail:                                    [FAILED]
[root@ssticentos65 ~]# /etc/init.d/sendmail start                 #启动sendmail服务
Starting sendmail:                                         [  OK  ]
Starting sm-client:                                        [  OK  ]
[root@ssticentos65 ~]#
```

## 外部邮箱直接看这里
1、安装邮件发送工具mailx
```bash
yum -y install mailx
```
2、配置mailx（配置外部邮箱需要外部邮箱开启pop imap smtp，具体可上网查找）
```bash
vim /etc/mail.rc

set from=detective19910204@163.com  #163注册的个人邮箱，用于zabbix服务端转发邮件到指定邮箱
set smtp=smtp.163.com				#填写163发送邮箱服务器地址smtp
set smtp-auth-user=detective19910204   #163个人邮箱账号
set smtp-auth-password=XXXXXXXXXXXX    #163个人邮箱开启的授权密码
set smtp-auth=login                    #登录
```
3、测试发送
```bash
echo "hello,world"|mailx -v -s "test" 1853711XXXX@wo.cn
```
然后直接在zabbix上管理→报警媒介类型→添加如下图所示
![图片](/assets/img/article_7/1.png "发送用邮箱")
**注意密码为开启的授权密码！！！**
最后在zabbix上管理→用户→超级管理用户→报警媒介→添加上收邮件的邮箱。建议使用手机邮箱还能同时收到短信提醒，简直完美！~
![图片](/assets/img/article_7/2.png "接收用邮箱")
参考：https://www.zhangqiongjie.com/1414.html