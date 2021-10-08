---
title: Linux系统操作笔记
date: 2019-12-27 23:22:40
tags: [操作系统,Linux]
---

目录
一、Linux操作系统
二、Vim文本编辑器
三、VMware Tools
四、VNC Server
附录

---

## 一、Linux操作系统（以下都以Ubuntu为例，其他版本Linux会注明）
#### 分区
以500G硬盘为例，大致分为以下分区
| 设备 | 类型 | 挂载点 | 大小 |
|---------|---------|---------|---------|
|/dev/sda1|swap| |8GB|
|/dev/sda2|efi| |99MB|
|/dev/sda3|ext4|/boot|1GB|
|/dev/sda4|ext4|/|150GB|
|/dev/sda5|ext4|/usr|80GB|
|/dev/sda6|ext4|/home|250GB|
<!--more-->
#### 初次登陆
安装Ubuntu时没注册root用户密码
```bash
sudo passwd root
```
第一次登陆系统，运行终端
```bash
sudo apt-get update
#更新同步/etc/apt/sources.list 和/etc/apt/sources.list.d 中列出的源的索引
#更新apt-get缓存中的软件包信息
#yum软件包管理工具使用yum update
sudo apt-get upgrade
#下载并安装在本系统上已有的软件包的最新版本
```
更改用户名
```bash
su root
sudo vim /etc/passwd #找到原用户名改掉
sudo vim /etc/shadow #找到原用户名改掉
cd /home
mv xxxx yyyy
sudo vim /etc/sudoers #在root	ALL=(ALL:ALL) ALL下加上
yyyy	ALL=(ALL:ALL) ALL
```
更改主机名
```bash
sudo vim /etc/hostname #找到原主机名改掉
sudo vim /etc/hosts #找到原主机名改掉
```

### 常见问题
#### APT工具的常用命令
```bash
apt-get install #下载并安装软件包#
apt-get upgrade #下载并安装在本系统上已有的软件包的最新版本#
apt-get remove #卸载特定的软件包#
apt-get source #下载特定的软件源代码#
apt-get clean #删除所有已下载的包文件#
```
#### APT工具删除卸载命令的详解
```bash
apt-get autoremove #新手最好不要在桌面版Ubuntu使用，删除已安装的软件包（保留配置文件），不会删除依赖软件包。（容易导致无法进入系统桌面）
apt-get remove #删除已安装的软件包（保留配置文件）
apt-get purge/apt-get --purge remove #删除已安装软件包（不保留配置文件），同时删除依赖软件包
apt-get clean #此命令会将 /var/cache/apt/archives/ 下的 所有 deb 删掉，相当于清理下载的软件安装包。
apt-get autoclean #删除为了满足某些依赖安装的，但现在不再需要的软件包。
```
#### 关于rpm软件管理工具以及高级软件包管理工具APT
下载的安装包文件格式为rpm的安装方式，通过命令终端：sudo rpm -i -v -h 文件名.rpm （一般-i就可以安装，-v代表显示rpm当前正在执行的工作，-h打印一些列“#”提醒用户当前的安装进度。sudo rpm -ivh 文件名.rpm效果一样）
sudo rpm -Uvh 文件名.rpm #升级软件包#
rpm -qa #列出系统中安装的所有软件包#
rpm -q 软件包名字 #列出安装的指定的软件包#
sudo rpm -e 软件包名字 #卸载指定的软件包#
***dpkg和rpm这些软件包管理器不能有效解决依赖性问题，所以有了以APT、yum等为代表的高级软件包管理工具。***

---

## 二、Vim文本编辑器
#### 进入编辑模式
按 i ：进入编辑模式，光标在原位置
按 I ：进入编辑模式，光标在行首位置
按 o：从光标所在行，下面一行开始编辑
按 O：从光标所在行，上面一行开始编辑
按 a ：从光标当前字符后编辑，
按 A ：从光标所在行的行尾编辑
#### 命令模式
：w 保存文本
：q 退出 vim
：w！强制保存，在root用户下，即使文本只读，也可以完成保存
：q！强制退出，所有改动不生效
：wq  保存退出
：set nu 显示行号
：set nonu 不显示行号
：nohl（hight light）不显示背景
：x  与 ：wq 作用一样，保存退出，但是：x 命令不会更改 mtime 的值；
：wq 命令会更改 mtime的值
参考<https://blog.csdn.net/wangshuang_2013/article/details/80724667>
#### vim强制保存
```bash
:w !sudo tee %
#w：表示保存文件
#！：表示执行外部命令
#tee：linux命令，这个有点复杂，可以查看linux命令帮助
#%：在执行外部命令时，%会扩展成当前文件名；这个%区别于替换时的%，替换时%的意义是代表整个文件，而不是文件名
```

### 常见问题
#### 只读文件的修改
```bash
sudo chmod a+w 文件名 #a代表所有用户，+代表添加某个权限，w代表可写。
```

---

## 三、VMware Tools

### 简介
经常使用虚拟机的童鞋肯定知道VMware虚拟机自带的这个工具，它主要用来本地和虚拟机直接的数据交互，比如最常用的粘贴板。

### 常见问题
#### Windows下安装VMware Tools
Windows下的安装最简单，只需要在菜单栏-虚拟机-安装VMware Tools就可以。这个软件是通过自带的iso文件加载到虚拟机的光驱设备中运行安装。
#### Linux下安装VMware Tools
Linux虚拟机中安装VMware Tools过程，必须进入虚拟机系统后再从菜单栏中选择虚拟机选项-安装VMware Tools。这时候会有一个iso文件加载到了虚拟机的光驱设备中，如果选择纯命令行的方法，就需要使用mount命令，但是一般虚拟机会自动加载到光驱内。
方法一（推荐）：
<img src="/assets/img/article_4/VMwareTools.png" width="75%">
方法二：将光驱挂载在/mnt目录下（需要根据具体的Linux版本约定的方式操作）。
列出挂载点目录的内容
```bash
ls mount-point
```
当然以上的步骤也可以直接在装有桌面的Linux上直接选择从光驱设备中复制粘贴出VMwareTools-x.x.x-yyyy.tar.gz
接下来解压缩安装包

```bash
tar zxf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz
或
tar zxpf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz
```
进入解压缩的目录后可以看到安装文件vmware-install.pl，使用命令安装
```bash
./vmware-install.pl
或
./vmware-install.pl -d #直接采取默认安装，否则需要按很多此回车
```
参考[官方文档][在Linux下安装vmwaer tools]
#### 在CentOS下安装过程中提示要先卸载open-vm-tools
```bash
rpm -qa |grep open-vm #找到要卸载的软件
rpm -e 软件名 #卸载后再安装
或
yum remove 软件名 #卸载再安装
```

---

## 四、VNC Server
附上一篇[本人CSDN博文链接][文章连接]

---

## 附录

### 常用Linux操作命令
查看系统版本
```bash
lsb_release -a #适用所有Linux发行版，但是Debian要安装lsb
cat /etc/issue #适用所有Linux发行版，显示发行版本信息
```
查看Linux内核版本
```bash
uname -a #显示用户及Linux版本号
cat /proc/version #显示正在运行的内核版本
```
查看cpu信息
```bash
cat /proc/cpuinfo
```
查看进程信息
```bash
ps #不带参数只查看当前进程
```

[文章连接]:https://blog.csdn.net/u010665718/article/details/89226720
[在Linux下安装vmwaer tools]:https://docs.vmware.com/cn/VMware-Workstation-Pro/14.0/com.vmware.ws.using.doc/GUID-08BB9465-D40A-4E16-9E15-8C016CC8166F.html