---
title: Windows系统之SSH服务
date: 2021-10-30 10:53:42
tags: [运维]
---

## 环境介绍
操作系统：Windows 10
软件版本：OpenSSH

---

## 开启SSH
右键开始菜单→设置→应用→可选功能（应用和功能）→添加功能→OpenSSH服务端→安装
右键此电脑→管理→服务和应用程序→服务→启动OpenSSH SSH Server

---
<!--more-->
## SecureCRT
使用SecureCRT连接输入Windows主机的IP，用户名密码为登录系统的用户名密码。初次连接如果报错keyboard-interactive authentication with the ssh2 server failed
查看对应连接地址的session option→Connection→SSH2→Authentication→将Password调整至第一的位置

## SecureFX
同样使用系统登录的账号密码登录
中文乱码需要打开C:\Users\用户名\AppData\Roaming\VanDyke\Config\Sessions中找到对应的地址.ini文件，将"Filenames Always Use UTF8"=00000000改为00000001。保存退出重新连接即可。