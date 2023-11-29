---
title: Linux系统之NFS服务
date: 2022-11-23 11:28:11
tags: [Linux,运维]
---

## 环境介绍
操作系统：Ubuntu 20.04.2 LTS，CentOS，Windows 10企业版
软件版本：nfs-kernel-server，nfs-common

## 一、NFS简介
NFS(Network File System)网络文件系统，它主要的功能是通过网络(局域网)，让不同的机器、不同的操作系统可以共享彼此的目录或文件，将共享的目录挂载到本地的文件系统，一般用来存储共享视频和图片等静态数据，常用在集群架构后端进行数据共享，NFS依赖负责信息传输的RPC协议。
<!--more-->
## 二、NFS入门
### 1、NFS服务软件
1. 需要开启mountd、nfsd、nlockmgr、portmapper、rquotad这5个服务， 除了nfsd和portmapper服务端口是固定的，其余都是随机分配的
2. nfsd：是nfs的守护进程，不负责文件存储（由NFS服务器本地内核负责调度存储），用于理解客户端发起的rpc请求，并将其转交给本地内核，而后存储在指定的文件系统上
3. mountd：mountd的服务端口是随机的，由rpc服务（portmapper）提供随机端口号，用于验证客户端是否在允许访问此NFS文件系统的客户端列表中，在则允许访问（发放一个令牌，持令牌去找nfsd），否则拒绝访问
4. dmapd：实现用户帐号的集中映射，把所有的帐号都映射为NFSNOBODY，但是在访问时却能以本地用户的身份去访问；
5. portmapper：NFS服务器的rpc服务，用于管理远程过程调用（RPC）
6. rpcbind：即RPC主程序

### 2、NFS常用目录或文件
1. /etc/exports：NFS主要配置文件
2. /usr/sbin/showmount：客户端的查看命令
3. /var/lib/nfs/etab：查看服务端配置目录的完整权限
4. /usr/sbin/exportfs：维护NFS分享资源的指令

### 3、端口
固定端口
NFS默认端口 2049 tcp/udp
portmapper默认端口 111 tcp/udp

随机端口
mountd、idmapd、rpcbind三个是随机端口，一般默认mountd为20048（需验证）
status和nlockmgr是随机端口
也可在配置文件中修改绑定固定端口/etc/sysconfig/nfs

参考https://www.cnblogs.com/ruhuanxingyun/p/14852570.html

## NFS服务端安装（Ubuntu为例）
### 1、准备工作
```bash
sudo mkdir /home/ayb/文档/NFSFile #创建一个共享文件夹
sudo chown -R 777 /home/ayb/文档/NFSFile #增加权限 -R代表递归此路径及下级路径
```
### 2、安装启动
```bash
sudo apt-get install nfs-kernel-server #安装NFS服务端
sudo systemctl enable nfs-server
sudo systemctl start nfs-server #启动服务
sudo systemctl status nfs-server #查看服务状态
```
### 3、配置NFS
```bash
sudo vim /etc/exports
#加入配置
/home/ayb/文档/NFSFile 172.20.*.*(rw,no_root_squash,sync) #允许172.20段访问
/home/ayb/文档/NFSFile 127.0.0.1(rw,no_root_squash,sync)#允许本机用于测试
```
重启服务
```bash
sudo systemctl restart nfs-server
showmount -e #显示NFS服务器上所有的共享目录
```
### 4、权限说明
|参数|作用|
|  ----  | ----  |
|ro|只读|
|rw|读写|
|root_squash|当NFS客户端以root管理员访问时，映射为NFS服务器端的匿名用户|
|no_root_squash|当NFS客户端以root管理员访问时，映射为NFS服务器端的root用户|
|all_squash|无论NFS客户端使用什么账户访问，均映射为NFS服务器的匿名用户|
|sync|同时将数据写入到内存与硬盘中，保证不丢失数据|
|async|优先将数据保存到内存，然后再写入硬盘；这样效率更高，但可能会丢失数据|
## NFS客户端安装（Windows及Linux）
### Windows 10
**只有Win10企业版具有此功能。专业版和家庭版需要使用第三方软件实现。**
控制面板→程序→启用或关闭Windows功能→NFS服务
Win+R打开命令行中输入

```bash
mount 172.21.1.1:/home/ayb/文档/NFSFile F:
#将NFS服务器的/home/ayb/文档/NFSFile挂载到F盘（F盘必须为没有使用的盘符）
```
### Linux
#### Ubuntu系统
```bash
sudo apt-get install nfs-common
```
#### CentOS系统
```bash
yum install nfs-utils -y
```
#### 创建客户端目录
```bash
mkdir ./NFSFile
```
#### 挂载
```bash
sudo mount -t nfs 172.21.1.1:/home/ayb/文档/NFSFile /mnt
#将NFS服务器的/home/ayb/文档/NFSFile挂载到/mnt
df -h
#检查本机客户端是否挂载成功
```
#### 卸载
```bash
sudo umount /mnt
#常见问题卸载时候提示device is busy
fuser -mv /mnt
#查看/mnt的占用程序进程ID
kill -9 进程ID
#再执行卸载
```
参考https://www.cnblogs.com/liqi175/p/16627100.html