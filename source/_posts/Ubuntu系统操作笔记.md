---
title: Ubuntu系统操作笔记
date: 2025-03-13 10:23:05
tags: [办公,操作系统,Linux]
---
## 环境介绍
操作系统：Ubuntu 20.04 server 64bit和Windows 10专业版22H2
软件版本：Chrome、RealVNC Viewer 7.7.0、TeamViewer、rdesktop、WPS

## 准备工作
所需工具：
U盘一个（最好8G以上）、Ubuntu ISO镜像一个、UltraISO。制作Ubuntu安装U盘

## 安装系统
自定义分区：
<!--more-->

<img src="/assets/img/article_4/ubuntu分区.PNG" width="50%">
设置好用户名和密码，系统第一次启动后，运行终端：sudo apt-get update 更新当前apt-get缓存中的软件包信息。

APT工具常用命令：
```bash
apt-get install #下载并安装软件包#
apt-get upgrade #下载并安装在本系统上已有的软件包的最新版本#
apt-get remove #卸载特定的软件包#
apt-get source #下载特定的软件源代码#
apt-get clean #删除所有已下载的包文件#
```

## 一、安装chrome浏览器
在官网下载linux版本，下载后的文件格式为deb。

安装方法1：
打开命令终端：sudo  dpkg  -i   文件名.deb
（dpkg -l 查看已安装的软件包、dpkg -r 卸载软件包）

安装方法2：
右键下载好的deb文件，选择“用 软件安装打开”，即可以安装。
注：若安装新版桌面版时候提示安装依赖问题，如libgconf-2-4等，直接运行sudo apt install -f即可解决

## 二、安装VNC远程软件

### 1、VNC下载地址
https://www.realvnc.com/en/connect/download/viewer/windows/

### Ubuntu设置
Ubuntu desktop 20.04自带了的GNOME桌面系统，这个Ubuntu默认的桌面是有桌面共享功能的，使用的就是Vino软件。Vino是GNOME桌面环境的默认组件，是VNC（虚拟网络计算）服务器，允许远程主机连接到Ubuntu 20.04的实际桌面。但是，最小化安装的Ubuntu不带Vino，是没有共享功能的。

#### 完整安装的系统不需要此操作
```bash
sudo apt-get update
sudo apt-get install vino #完整安装的Ubuntu默认已经安装好
```

#### 设置方法
打开 设置–>共享（Setting–>Sharing），安装完vino后会出现屏幕共享（Screen Sharing）选项，打开共享（Sharing）和屏幕共享（Screen Sharing），设置访问密码之后即可。

方法1：命令行输入以下命令
```bash
gsettings set org.gnome.Vino require-encryption false
```

方法2：安装 dconf-editor 系统配置编辑器，修改/org/gnome/desktop/remote-access
打开一个终端，输入下面的命令，安装系统配置编辑器
```bash
sudo apt-get install dconf-editor
```
打开 dconf-editor在桌面右下方图标里找
按照如下路径org->gnome->desktop->remote access->require encrytion把原先默认的开启状态改为关闭
最后打开Windows上安装好的VNC，输入IP地址就可以正常连接

参考网站：https://www.cnblogs.com/kebibuluan/p/17851577.html

## 三、安装其它远程软件

### 安装teamview
在官网下载linux版本，下载后的文件格式也为deb。安装方法同上。使用方法跟windows版本的一毛一样。

### 安装向日葵远程
在官网下载linux版本，下载后的文件格式为tar.gz。将文件解压缩出来，阅读README文件，里面有软件的详细使用介绍。根据内容可知，分为绿色版本和安装版本，都为.sh文件的脚本形式运行。使用绿色版本，运行script脚本“./run.sh”。使用安装版本，运行script脚本“./install.sh”。你最终会发现，它只能被远程控制，没有远程控制windows电脑的功能。哈哈哈哈哈！

后续版本的向日葵有deb版本了，下载后安装方法一样，不过新版本的都要登录才能远控其他电脑。

### 安装rdesktop
如果想用linux电脑通过远程桌面的形式远程Windows电脑，安装这个软件就可以。
注意：Windows10系统需要在Windows远程桌面设置里，取消勾选需要计算机使用网络级别身份验证集进行连接（建议）。
下载后的文件格式为tar.gz。将文件解压缩出来。有些linux版本会自带此软件，不需要另外安装。同理，解压出来的文件夹里有README文件，仔细阅读使用说明。可以看到此软件也是通过script脚本运行安装。

Ubuntu可以直接运行安装命令
```bash
sudo apt-get install rdesktop
```
使用方法：输入命令 rdesktop ip:port（默认3389可不用写） -u administrator -p secretnumber -a 16 -g 1360*768(分辨率) -r sound:local -5

参考网址：https://blog.csdn.net/cunjiu9486/article/details/109073104

## 四、安装办公软件

### 安装typora
目前只有安装0.11.18版本可以避免激活问题

### 安装WPS
从官网下载deb格式
需要安装wps字体来解决字体显示问题