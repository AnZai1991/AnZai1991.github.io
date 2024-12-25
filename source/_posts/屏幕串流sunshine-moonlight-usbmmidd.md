---
title: 屏幕串流sunshine+moonlight+usbmmidd
date: 2024-12-25 14:28:06
tags: [杂记]
---
## 安装SunShine
下载地址：https://github.com/LizardByte/Sunshine/releases
支持Mac、Windows、Linux三个平台
安装完成后，右击右下角图标打开，在浏览器里开始配置
### 使用
创建用户名密码
sunshineaz\az1xx1
configuration里修改为中文，保存应用重启
远程串流需要打开网络里的UPnP
ip地址族选择ipv4+ipv6
<!--more-->
## 安装moonlight
下载地址：https://github.com/moonlight-stream
### 使用
手动添加ip地址，会弹出PIN码
在sunshine上输入PIN码即可串流

## 安装usbmmidd
下载地址：https://www.cnblogs.com/zspace/p/17069763.html
### 使用
解压后，使用cmd命令行进入到usbmmid_v2目录下执行
```bash
deviceinstaller64 install usbmmidd.inf usbmmidd
deviceinstaller64 enableidd 1 #开启虚拟屏
deviceinstaller64 enableidd 0 #关闭虚拟屏
```
提供两个方便的批处理
```bash
start cmd /c deviceinstaller64.exe enableidd 1
```
```bash
start cmd /c deviceinstaller64.exe enableidd 0
```
分辨率的修改方法，进入注册表修改
计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WUDF\Services\usbmmIdd\Parameters\Monitors

cmd进入sunshine安装目录C:\Program Files\Sunshine\tools
输入下面命令回车可以查看到虚拟屏幕的名称
```bash
.\dxgi-info.exe
```
将此名称复制粘贴到sunshine配置中视频/音频下的输出名称
保存应用
参考地址：https://blog.csdn.net/qq_40863153/article/details/131742207

## 优化点
打开Windows设置搜索“贴靠”
找到相关设置，将贴靠窗口自动调整关闭掉

避免开机自启，将sunshine.exe重命名，建一个文本文件命名为sunshine.exe

## 类似虚拟显示器软件
EasyVirtualDisplay下载地址：https://github.com/KtzeAbyss/Easy-Virtual-Display