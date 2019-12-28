---
title: Kali Linux入门实验
date: 2019-12-28 13:57:01
tags: Linux
---

## ARP欺骗
```bash
arpspoof -i eth0 -t 192.168.1.2 192.168.1.1
```
### 常见问题
提示没有arpspoof命令
首先要对Kali系统更新源，查看系统的更新源地址文件
```bash
cat /etc/apt/sources.list
```

#kali官方源
deb http://http.kali.org/ moto main non-free contrib 
deb-src http://http.kali.org/ moto main non-free contrib
<!--more-->
deb http://security.kali.org/ moto/updates main contrib non-free 
deb-src http://security.kali.org/ moto/updates main contrib non-free

#中科大kali源 
deb http://mirrors.ustc.edu.cn/kali kali main non-free contrib 
deb-src http://mirrors.ustc.edu.cn/kali kali main non-free contrib 
deb http://mirrors.ustc.edu.cn/kali-security kali/updates main contrib non-free

更新系统源之后，安装dsniff网络审计和渗透工具集合
```bash
apt install dsniff ssldump
```
Kali更新源参考：<https://www.fujieace.com/kali-linux/update-source.html>

---

## setoolkit
setoolkit是一款模拟网络攻防的社会实验工具。

---