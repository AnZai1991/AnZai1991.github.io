---
title: Linux系统操作笔记
date: 2019-12-27 23:22:40
tags: [操作系统,Linux]
---

目录
一、Linux操作系统
二、Vim文本编辑器
三、SecureCRT
四、VMware Tools
五、VNC Server
六、设置SSH通过密钥登陆
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
````
#### 开启SSH
```bash
dpkg -l |grep ssh #在已安装的程序中查找是否有ssh
sudo ps -ef |grep ssh #在进程中查看ssh是否安装，其中ssh是客户端、sshd是服务端。e代表显示所有进程，f代表全格式
sudo apt-get install openssh-server #安装openssh
service sshd start #启动ssh服务端，也可能是service ssh start
service sshd stop #停止ssh服务端，也可能是service ssh stop
```
```bash
#以下是CentOS为例子演示开启SSH
rpm -qa |grep ssh #在已安装软件包中查看ssh是否安装
yum install openssh-server #安装openssh
systemctl start sshd.service #开启ssh服务
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
#### 部分Linux不支持root通过ssh远程登陆
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
#### 开启SSH后使用工具无法连接
首先需要去看防火墙的状态，关闭防火墙后再试。
CentOS查看防火墙
```bash
systemctl status firewalld.service #查看防火墙状态
systemctl stop firewalld.service #停止防火墙
systemctl disable firewalld.service #禁止防火墙随系统启动
systemctl start firewalld.service #开启防火墙
systemctl enable firewalld.service #使防火墙随系统启动
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
#### 初次连接报错
初次使用SecureCRT没有配置两个文件，就会报如下错误。
密钥交换失败。没有兼容的密钥交换方法。服务器支持以下方法：ecdh-sha2-nistp256、ecdh-sha2-nistp384、ecdh-sha2-nistp521、diffie-hellman-group-exchange-sha256
#### SecureCRT显示中文乱码解决方法
菜单栏里-选项-会话选项-外观
字符编码选项由默认改为UTF-8保存即可。
#### 设置SSH远程登陆超时的时间（SUSE Linux下）
```bash
echo "export TMOUT=900" >> /etc/profile #设置15分钟超时写入配置文件，为0表示不超时
cat /etc/profile |grep TMOUT -n #查询设置结果
```

---

## 四、VMware Tools

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

## 五、VNC Server
附上一篇[本人CSDN博文链接][文章连接]

---

## 六、设置SSH通过密钥登陆
###  原理
利用密钥生成器制作一对密钥——一只公钥和一只私钥。将公钥添加到服务器的某个账户上，然后在客户端利用私钥即可完成认证并登录。这样一来，没有私钥，任何人都无法通过 SSH 暴力破解你的密码来远程登录到系统。此外，如果将公钥复制到其他账户甚至主机，利用私钥也可以登录。

### 操作步骤
#### 1、制作密钥对（分Linux和Windows）
**Linux环境**
在要用密码登录的**Linux**服务器上制作，登录上后执行以下命令：
```bash
ssh-keygen #建立密钥对
#Generating public/private rsa key pair.
#Enter file in which to save the key (/root/.ssh/id_rsa): 按Enter
#Created directory '/root/.ssh'.
#Enter passphrase (empty for no passphrase): 输入密钥锁码，或直接回车留空
#Enter same passphrase again: 再次输入
#Your identification has been saved in /root/.ssh/id_rsa. 私钥
#Your public key has been saved in /root/.ssh/id_rsa.pub. 公钥
#The key fingerprint is:
#aa:bb:cc:dd:ee:ff:00:11:22:33:44:55:66:77:88:99 root@host
```
密钥锁码在使用私钥时必须输入，这样可以保护私钥不被盗用。当然也可以留空，实现无密码登录。
现在root用户的主目录中生成了一个.ssh的隐藏目录，内含两个密钥文件。id_rsa为私钥，id_rsa.pub为公钥。

**Windows环境**
最近的Windows 10开始支持openssh，之前的Windows都需要依赖第三方工具来生成，一般最常见的是通过git工具来生成，还有一种可以通过putty生成。
Windows 10下首先要确认是否安装了openssh， 依次在“设置”-“应用”-“管理可选功能”。 如果在出现的列表中没有看到“OpenSSH Client”，单击“添加功能”按钮并进行安装。 安装后可能需要重新启动PC。
安装OpenSSH后， 从“开始”菜单中打开命令提示符。 在终端窗口中输入“ssh-keygen”，然后按Enter键。 系统将提示您确认保存位置。 建议按Enter键以使用用户目录中的默认位置。 否则，键入要保存密钥的路径，然后按Enter键。现在可以选择向密钥添加密码。 如果你添加一个，你需要在使用密钥时提供它。 输入密码并按Enter键或立即按Enter键继续输入密码短语。
Windows现在将生成的RSA公钥/私钥对。 公钥将在指定的目录中存储为“id_rsa.pub”。 将此密钥上传到需要SSH的任何计算机。 然后，可以使用Windows的内置SSH客户端打开连接 - 键入“ssh user @ hostname”以使用生成的凭据进行连接和身份验证。
#### 2、在需要登录的服务器上安装公钥
键入以下命令，在服务器上安装公钥：
```bash
cd .ssh
cat id_rsa.pub >> authorized_keys
```
公钥安装完成，为了确保连接成功，确认一下文件的权限正确：
```bash
chmod 600 authorized_keys
chmod 700 ~/.ssh
```
#### 3、设置SSH，打开密钥登录功能
编辑/etc/ssh/sshd_config文件
```bash
RSAAuthentication yes #RSA认证
PubkeyAuthentication yes #公钥认证
PermitRootLogin yes #允许root登录
```
完成全部设置，并以密钥方式登录成功后，再禁用密码登录
```bash
PasswordAuthentication no
```
最后，重启SSH服务
```bash
service sshd restart
```
#### 4、将私钥下载到客户端，然后转换为putty能使用的格式
使用FTP工具将私钥文件id_rsa下载到客户端，打开 PuTTYGen，单击 Actions 中的 Load 按钮，载入你刚才下载到的私钥文件。如果你刚才设置了密钥锁码，这时则需要输入。
载入成功后，PuTTYGen 会显示密钥相关的信息。在 Key comment 中键入对密钥的说明信息，然后单击 Save private key 按钮即可将私钥文件存放为 PuTTY 能使用的格式。
今后，当你使用 PuTTY 登录时，可以在左侧的 Connection -> SSH -> Auth 中的 Private key file for authentication: 处选择你的私钥文件，然后即可登录了，过程中只需输入密钥锁码即可。

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