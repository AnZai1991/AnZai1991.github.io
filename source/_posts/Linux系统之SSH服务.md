---
title: Linux系统之SSH服务
date: 2021-08-17 17:03:37
tags: [操作系统,Linux]
---

## 环境介绍
操作系统：Ubuntu 20.04，CentOS 8，SUSE Linux SP3，Debian
软件版本：openssh

---

## Ubuntu开启SSH服务
使用**Ctrl+Alt+T**打开终端，首先确定系统是否自带的有SSH工具
```bash
dpkg -l |grep ssh #在已安装的程序中查找是否有ssh
sudo ps -ef |grep ssh #在进程中查看ssh是否启动，其中ssh是客户端、sshd是服务端。e代表显示所有进程，f代表全格式
```
<!--more-->
发现系统自带的只有openssh-client，因为要做服务端，所以需要安装openssh-server
```bash
sudo apt-get update
sudo apt-get install openssh-server

sudo systemctl status ssh #查看ssh状态
service sshd start #启动ssh服务端，也可能是service ssh start
service sshd stop #停止ssh服务端，也可能是service ssh stop
```

## CentOS开启SSH服务
```bash
rpm -qa |grep ssh #在已安装软件包中查看ssh是否安装
yum install openssh-server #安装openssh
systemctl start sshd.service #开启ssh服务
```
### 常见问题
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

## SecureCRT
#### 初次使用
使用root用户，变更ssh客户端配置
```bash
cd /etc/ssh
vim ssh_config
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
#### 设置SSH远程登陆超时的时间（以SUSE Linux下为例）
```bash
echo "export TMOUT=900" >> /etc/profile #设置15分钟超时写入配置文件，为0表示不超时
cat /etc/profile |grep TMOUT -n #查询设置结果
```

## 设置SSH通过密钥登陆
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