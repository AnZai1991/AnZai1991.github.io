---
title: SUSE Linux开启FTP服务
date: 2020-01-20 20:25:22
tags: Linux
---

1、关闭防火墙
```bash
service SuSEfirewall2_setup stop
service SuSEfirewall2_init stop
chkconfig --list |grep firewall #查看防火墙开机启动状态
chkconfig SuSEfirewall2_init off
chkconfig SuSEfirewall2_setup off
```
2、安装FTP
```bash
zypper install -y vsftpd
```
3、修改配置
<!--more-->
```bash
vim /etc/vsftpd.conf
# line 18: uncomment ( allow writing )，开启任何用户的写权限
write_enable=YES    
# line 39: uncomment
ls_recurse_enable=YES
# line 59: uncomment ( allow local user ) ，开启本地用户登录服务
local_enable=YES
# line 69: uncomment ( enable chroot )
chroot_local_user=YES
# line 75: uncomment ( enable chroot list )
chroot_list_enable=YES
# line 79: uncomment ( specify chroot list )
chroot_list_file=/etc/vsftpd.chroot_list
# line 91: 不启用匿名访问
anonymous_enable=NO   
# line 200: uncomment: ( allow ascii mode )
ascii_upload_enable=YES
ascii_download_enable=YES
# add at the bottom: specify  your root directory
local_root=/home
# use local time
use_localtime=YES
```
4、配置FTP登录用户
```bash
vim /etc/vsftpd.chroot_list
root
```
5、重启
```bash
service vsftpd start #启动FTP服务
service vsftpd stop #关闭FTP服务
service vsftpd restart #重启FTP服务
chkconfig vsftpd on #开机启动
chkconfig vsftpd off #开机不启动
```