---
title: iPerf3测试无线Wifi
date: 2020-09-03 21:37:56
tags: 网络
---

## 环境搭建
1、准备一台PC，一部手机，及要测试的无线路由器。将手机和PC都链接至此无线路由器的局域网内
2、去官网下载iPerf测试工具https://iperf.fr/iperf-download.php
3、在手机端安装HE NET Network Tools，这是官方推荐的手机端软件

--

## 开始测试
1、在PC上，运行→cmd→ipconfig/all查看本地分配的IP地址，假设为192.168.124.2。
2、打开Windows10的PowerShell或命令提示符，把iperf3.exe拖进去，然后后面加上" -s"就将PC作为服务端开始运行。
3、在手机端打开APP，选择iperf3功能页，在框里输入命令
```bash
-c 192.168.124.2 -i 1 -t 30
```
<!--more-->
iperf3常见的参数解释：
-c host以client模式启动
-i SEC 以秒为单位显示报告间隔
-t 测试时间，默认10秒
-p 指定服务器端使用的端口或客户端所连接的端口
-w 指定TCP窗口大小，默认是8KB

4、此时在手机端和PC端可以看到测试的结果，能够看到最大带宽和实时的网速。