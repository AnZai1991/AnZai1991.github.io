---
title: Linux系统之Samba服务
date: 2021-01-13 15:47:25
tags: [Linux,运维]
---

## 环境介绍
操作系统：Ubuntu 20.04.2 LTS，Windows10
软件版本：Samba

---

## 操作步骤
### 安装及配置
```bash
sudo apt-get install samba-common samba #安装samba
vim /etc/samba/smb.conf #进行配置
```
<!--more-->
在配置文件末尾加入：
```bash
[share] #共享目录名，这个名字可以随意设置，会显示在Windows界面上的名称
comment = Linux Share #设置这个共享目录的描述
path = /home/az #设置共享目录的路径
public = yes 
writeable = yes #设置是否可写
browseable = yes #设置是否可在网上邻居中可见
guest ok = yes #设置是否允许匿名访问
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
### 在Windows系统上进行访问
Windows10上的匿名访问
此时，若是win10系统，win+R运行→输入"\\192.168.0.103"→回车
结果是看不到共享的目录的，提示“你不能访问此共享文件夹,因为你组织的安全策略阻止未经身份验证的来宾访问。”
win+R运行→gpedit.msc→计算机配置→管理模板→网络→Lanman工作站→启用不安全的来宾登录→确定
此时，问题解决，可以访问到共享的samba目录。