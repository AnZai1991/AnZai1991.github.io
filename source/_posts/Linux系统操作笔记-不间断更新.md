---
title: Linux系统操作笔记---不间断更新
date: 2019-12-27 23:22:40
tags: Linux
---

## 一、Linux操作系统（以下以Ubuntu为例）
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
第一次登陆系统，运行终端
```bash
sudo apt-get update
#更新同步/etc/apt/sources.list 和/etc/apt/sources.list.d 中列出的源的索引
#更新apt-get缓存中的软件包信息
sudo apt-get upgrade
#下载并安装在本系统上已有的软件包的最新版本
````

#### 开启SSH
```bash
sudo ps -e |grep ssh #查看ssh是否安装，其中ssh是客户端、sshd是服务端
sudo apt-get install openssh-server #安装openssh
service sshd start #启动ssh服务端，也可能是service ssh start
service sshd stop #停止ssh服务端，也可能是service ssh stop
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
#### 关于rpm软件管理工具以及高级软件包管理工具APT
下载的安装包文件格式为rpm的安装方式，通过命令终端：sudo rpm -i -v -h 文件名.rpm （一般-i就可以安装，-v代表显示rpm当前正在执行的工作，-h打印一些列“#”提醒用户当前的安装进度。sudo rpm -ivh 文件名.rpm效果一样）
sudo rpm -Uvh 文件名.rpm #升级软件包#
rpm -qa #列出系统中安装的所有软件包#
rpm -q 软件包名字 #列出安装的指定的软件包#
sudo rpm -e 软件包名字 #卸载指定的软件包#
***dpkg和rpm这些软件包管理器不能有效解决依赖性问题，所以有了以APT、yum等为代表的高级软件包管理工具。***

#### 不支持root通过ssh远程登陆
有些Linux是不支持root用户使用ssh远程登陆的，比如Debian、Kali（采用Debian内核）
修改配置文件/etc/ssh/sshd_config
1、确认一下
#PasswordAuthentication yes #kali 默认情况下,该取值为 yes,不用修改
2、找到
#PermitRootLogin prohibit-password
并修改为
PermitRootLogin yes
3、重启ssh服务
```bash
/etc/init.d/ssh restart
```
4、设置ssh开机自启动
```bash
update-rc.d ssh enable
```

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

### 常见问题
#### 只读文件的修改
```bash
sudo chmod a+w 文件名 #a代表所有用户，+代表添加某个权限，w代表可写。
```

---

## 三、SecureCRT
#### 初次使用

使用root用户，变更ssh客户端配置
```bash
cd /etc/ssh
vi ssh_config
#取消如下两行注释
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
```
使用root用户，变更ssh服务端配置
```bash
vi sshd_config
#复制上述两行到sshd_config中。如果有其它密钥交换语句，则注释掉。
```
最后重启系统。

### 常见问题
初次使用SecureCRT没有配置两个文件，就会报如下错误。
密钥交换失败。没有兼容的密钥交换方法。服务器支持以下方法：ecdh-sha2-nistp256、ecdh-sha2-nistp384、ecdh-sha2-nistp521、diffie-hellman-group-exchange-sha256

SecureCRT显示中文乱码解决方法：
菜单栏里-选项-会话选项-外观
字符编码选项由默认改为UTF-8保存即可。

---

## 四、VMware Tools
### 简介
经常使用虚拟机的童鞋肯定知道VMware虚拟机自带的这个工具，它主要用来本地和虚拟机直接的数据交互，比如最常用的粘贴板。
Windows下的安装最简单，只需要在菜单栏-虚拟机-安装VMware Tools就可以。这个软件是通过自带的iso文件加载到虚拟机的光驱设备中运行安装。
主要讲一下Linux虚拟机中安装VMware Tools过程，必须进入虚拟机系统后再从菜单栏中选择虚拟机选项-安装VMware Tools。这时候会有一个iso文件加载到了虚拟机的光驱设备中，如果选择纯命令行的方法，就需要使用mount命令，将光驱挂在到/mnt目录下（需要根据具体的Linux版本约定的方式操作）。
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



[在Linux下安装vmwaer tools]:https://docs.vmware.com/cn/VMware-Workstation-Pro/14.0/com.vmware.ws.using.doc/GUID-08BB9465-D40A-4E16-9E15-8C016CC8166F.html