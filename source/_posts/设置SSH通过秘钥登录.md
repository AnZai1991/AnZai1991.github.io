---
title: 设置SSH通过秘钥登录
date: 2020-01-20 20:12:13
tags: Linux
---

###  原理
利用密钥生成器制作一对密钥——一只公钥和一只私钥。将公钥添加到服务器的某个账户上，然后在客户端利用私钥即可完成认证并登录。这样一来，没有私钥，任何人都无法通过 SSH 暴力破解你的密码来远程登录到系统。此外，如果将公钥复制到其他账户甚至主机，利用私钥也可以登录。

### 操作步骤
#### 制作密钥对（分Linux和Windows）
**Linux**
在要用密码登录的Linux服务器上制作，登录上后执行以下命令：
<!--more-->
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
**Windows**
Windows环境下，从最近的Windows 10开始支持openssh，之前的Windows都需要依赖第三方工具来生成，一般最常见的是通过git工具来生成，还有一种可以通过putty生成。
Windows 10下首先要确认是否安装了openssh， 启动“设置”应用，然后单击“应用”类别。 接下来，单击“可选功能”链接。 在出现的列表中会看到“OpenSSH Client”，请单击“添加功能”按钮并进行安装。 安装后可能需要重新启动PC。
安装OpenSSH后， 从“开始”菜单中打开命令提示符。 在终端窗口中输入“ssh-keygen”，然后按Enter键。 系统将提示您确认保存位置。 我们建议按Enter键以使用用户目录中的默认位置（一般是C:\Users\Administrator目录下）。 否则，键入要保存密钥的路径，然后按Enter键。现在可以选择向密钥添加密码。 如果你添加一个，你需要在使用密钥时提供它。 输入密码并按Enter键或立即按Enter键再次输入密码。
Windows现在将生成的RSA公钥/私钥对。 公钥将在指定的目录中存储为“id_rsa.pub”。 将此密钥上传到需要SSH的任何计算机。 然后，可以使用Windows的内置SSH客户端打开连接 - 键入“ssh user @ hostname”以使用生成的凭据进行连接和身份验证。
#### 在需要登录的服务器上安装公钥
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
#### 设置SSH，打开密钥登录功能
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
#### 将私钥下载到客户端，然后转换为putty能使用的格式
使用FTP工具将私钥文件id_rsa下载到客户端，打开 PuTTYGen，单击 Actions 中的 Load 按钮，载入你刚才下载到的私钥文件。如果你刚才设置了密钥锁码，这时则需要输入。
载入成功后，PuTTYGen 会显示密钥相关的信息。在 Key comment 中键入对密钥的说明信息，然后单击 Save private key 按钮即可将私钥文件存放为 PuTTY 能使用的格式。
今后，当你使用 PuTTY 登录时，可以在左侧的 Connection -> SSH -> Auth 中的 Private key file for authentication: 处选择你的私钥文件，然后即可登录了，过程中只需输入密钥锁码即可。