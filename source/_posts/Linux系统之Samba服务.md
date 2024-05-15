---
title: Linux系统之Samba服务
date: 2021-01-13 15:47:25
tags: [Linux,运维]
---

## 环境介绍
操作系统：Ubuntu 20.04.2 LTS，Windows10
软件版本：Samba

---

## 简介
Samba服务所使用的端口和协议:
1)Port 137(UDP)-NetBl0S 名字服务;nmbd
2)Port 138(UDP)- NetBlOS 数据报服务
3)Port 139(TCP)-文件和打印共享: smbd(基于SMB(Server Message Block)协议，主要在局域网中使用，文件共享协议)
4)Port 389(TCP)-用于 LDAP (Active Directory Mode)
5)Por 445(TCP)-NetBIOS服务在windos 2000及以后版本使用此端口,(Common Internet File System，ClFS，它是SMB协议扩展到Internet后，实现Internet文件共享)
6)Port 901(TCP)-用于 SWAT，用于网页管理Samba
<!--more-->
## 操作步骤
### 安装及配置
```bash
sudo apt-get install samba-common samba #安装samba
vim /etc/samba/smb.conf #进行配置
```
默认使用445端口，因特殊情况被禁用，可以更改端口号，例如改为6445，在global中添加
```bash
[global]
smb ports = 6445
```
在配置文件末尾加入（注意：后面的注释说明要**删掉**否则启动报错）：
```bash
[share] #共享目录名，这个名字可以随意设置，会显示在Windows界面上的名称
comment = Samba on Ubuntu #设置这个共享目录的描述
path = /home/az #设置共享目录的路径
writeable = yes #设置是否可写
browseable = no #设置是否可在网上邻居中可见，默认yes
guest ok = no #设置是否允许匿名访问不需要提供用户名密码，默认no。此配置项等同public
```
登录samba用户的设置
```bash
sudo useradd sambauser #添加一个名为sambauser的用户，也可用系统已有用户
sudo smbpasswd -a root #为root添加一个samba密码，此密码独立于系统用户密码

pdbedit -L #列出samba用户列表
pdbedit -Lv #列出samba用户详细信息
pdbedit -Lw #列出smbpasswd格式的用户
pdbedit -a abc #增加用户abc
pdbedit -x abc #删除用户abc
pdbedit –c "[D]" –u abc #暂停用户abc
pdbedit –c "[]" –u abc #恢复用户abc
```
### 启动Samba
启动samba服务
```bash
sudo /etc/init.d/smbd start #注意这里使用的是smbd，不是smb也不是samba
```
### 在Windows系统上使用默认445端口进行访问
Windows10上的非匿名访问
此时，若是win10系统，win+R运行→输入"\\192.168.0.103"→回车
根据提示输入上面创建的sambauser用户名密码。

Windows10上的匿名访问方法一样，只是不需要输入用户名密码
但是可能出现看不到共享的目录的，提示“你不能访问此共享文件夹,因为你组织的安全策略阻止未经身份验证的来宾访问。”
win+R运行→gpedit.msc→计算机配置→管理模板→网络→Lanman工作站→启用不安全的来宾登录→确定
此时，问题解决，可以访问到共享的samba目录。

### 在Windows系统上使用非445端口进行访问，通过Windows端口映射的方法
在管理员权限下的cmd中执行
```bash
#添加代理映射
netsh interface portproxy add v4tov4 listenport=445 listenaddress=127.0.0.1 connectport=6445 connectaddress=172.21.26.84
#查看
netsh interface portproxy show all
#删除代理映射
netsh interface portproxy delete v4tov4 listenport=445 listenaddress=127.0.0.1
#清空代理映射
netsh interface portproxy reset
```
在控制面板-程序功能-启用或关闭windows功能开启SMB客户端，关闭SMB服务器(否则可能通过127.0.0.1访问到的是本地的samba共享目录)。需要重启Windows
win+R运行→输入"\\127.0.0.1"→回车
参考：https://blog.csdn.net/babytang008/article/details/120489133