---
title: Windows系统操作笔记
date: 2023-11-29 11:24:19
tags: [办公]
---

## Windows系统通用技巧

查看端口占用
netstat -ano
查看端口占用的进程号
netstat -aon|findstr ""
<!--more-->
根据进程号找进程
tasklist|findstr ""
根据映像名称查找进程
tasklist /fi "imagename eq XX.exe"
结束进程
taskkill /f /t /im XX.dll

## Windows8

如何更改win8用户名？
运行netplwiz