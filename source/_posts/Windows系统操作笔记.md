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

查看链接过的ssid
netsh wlan show profiles
查看连接过的ssid的密码
netsh wlan show profile name="MyWiFi" key=clear
删除连接过的ssid
netsh wlan delete profile name="MyWiFi" 

## Windows8

如何更改win8用户名？
运行netplwiz

## Windows11
Windows 11 更新后，可以直接在设置里查看当前已连接 Wi-Fi 的密码：
打开 “设置”（快捷键：Win + I）。
点击左侧的 “网络和 Internet”。
点击 “Wi-Fi”。
点击 “已知网络管理”。
在列表中找到并点击你想查看的 Wi-Fi 名称。
在“Wi-Fi 网络安全密钥”一栏，点击旁边的 “查看” 按钮（眼睛图标），即可显示密码