---
title: Bourne Again Shell命令
date: 2020-10-19 18:36:44
tags: Linux
---

## Shell基本命令
### 查看目录和文件
```bash
pwd   #显示当前目录
cd   #改变目录
cd ..   #进入上一级目录
cd ../..   #进入上上一级目录，以此类推
cd   #回到用户主目录
cd ~   #回到用户主目录
ls   #列出目录内容
ls -F   #目录后会加上/，可执行文件后加*，链接文件后加上@
ls -a   #显示所有文件（包含隐藏文件）
ls -l   #查看文件的各种属性
```
<!--more-->	
总共有8个不同的信息栏。从左到右依次表示：
文件的权限标志、文件的链接个数、文件所有者的用户名、该用户所在的用户组名、文件的大小、最后一次被修改时的日期、最后一次被修改时的时间、文件名

重点解释下文件的权限标志和文件的链接个数
文件的权限标志：
列如：-rwxr-xr-x
第1位的"-"表示文件类型，后面是3组权限位，"rwx""xr-""r-x"分别代表属主、属组和其他人的权限。r表示可读取，w表示可写，x表示可执行。
ls -ld   #查看当前目录文件夹的各种属性

文件的链接个数：
分硬链接和软链接两种，使用ln -s target link_name 创建的是软链接，同时也可以为目录创建。类似于windows里的快捷方式
ln target link_name创建的是硬链接，修改任一文件或目录，同时会对两个文件或目录生效。硬链接实际用的很少，普遍使用软链接。


### 列出目录内容
```bash
dir   #与Windows系统一样
vdir   #相当于ls -l
```

### 查看文本文件
```bash
cat   #查看文件内容，通常是一个文本文件，也可以跟多个文件名作为参数。当然也可以用通配符
cat -n   #在查看的同时每一行前显示行号
more   #不会直接显示所有的内容，空格键翻动一页，按Enter向下滚动一行，Q键退出。
```

### 阅读文件的开头和结尾
```bash
head -n   #加上数字参数和文件名参数，显示此文件的头几行
tail -n   #加上数字参数和文件名参数，显示此文件的后几行
```

### 更好的文本阅读工具
```bash
less   #空格键下一页，B键上一页，Q键退出，"/"后跟需要查找的内容
less -M   #底部输出额外的文件阅读信息
```

### 查找文件内容
```bash
grep [options] PATTERN [file]   #显示文件中包含某字符的行，如果要查询关键词需要加上单引号
egrep   #通grep一样
```

### 查找文件
```bash
find /etc/ -name init.d -type d -print   #跟路径，跟名称，跟类型，打印
find /usr/bin/ -type f -atime +100 -print   #跟路径，跟类型，最近一次修改在100天前的文件（100天内没修改过的文件），跟打印
find . -type f -mtime -1 -print   #跟路径，跟类型，跟最近一天内修改的文件
```

### 更快的定位文件
```bash
locate *.doc   #检索文件名数据库，需要更新的话使用root权限执行updatedb
```

### 从终端运行程序
```bash
dpkg -l |grep chrome
google-chrome-stable   #终端挂起，运行chrome浏览器，直到被关闭
google-chrome-stable &   #在后台运行，终端继续等待接收用户输入，并提示进程号
```

### 查找特定程序
```bash
whereis find   #返回这个命令的二进制可执行文件位置、源代码文件和使用手册位置
whereis -b find   #只返回可执行文件位置
```

### 寻求帮助
```bash
man   #跟命令，获取命令和系统调用帮助手册
whatis   #跟命令，返回命令简介
apropos   #跟关键词，用来查找相关的命令，跟whatis作用相反
```

---

## 文件目录管理
```bash
mkdir   #建立目录
mkdir -p ~/temp/job   #加-p可以直接创建完整的目录，先创建temp再创建job
touch #创建空白文件。也有更新文件建立日期和时间的功能
```

### 移动、复制和删除
```bash
mv hello bin/
mv photos/ 桌面/ #既可以移动文件也可以移动目录，同时对其重命名！
mv -i #此选项用于防止直接覆盖文件，若有同名文件或文件夹，将提示是否覆盖
mv -b #此选项同样是解决同名覆盖的问题，会在同名的文件后加上~
```

### 复制文件和目录
```bash
cp test.php test/
cp -i #与mv相同，为了防止同名覆盖问题
cp -b #与mv相同，为了解决同名覆盖，会在文件或目录后加上~

cp test/ 桌面/ #这会将test目录里的内容复制到桌面路径下，但是不会复制test目录本身
cp -r test/ 桌面/ #此选项会将子目录及其内容一起复制到另一个子目录下
```

### 删除目录和文件
```bash
rmdir #跟目录名，删除此目录，且只能删除空白目录
rm test/*.php #删除test目录下所有的php文件
rm -i #此选项用于逐个确认是否删除，对于只读文件，加不加-i都会提示是否删除

rm -f #避免提示，默认全部同意删除
rm -r #删除目录下所有的文件和子目录
rm -rf #谨慎使用，有一个更安全的命令delete

delete #
```
### 改变文件所有权
```bash
sudo chown ayb:root test.php #将test.php的文件所有者改为ayb，所属组改为root组
sudo chown :root test.php #保留test.php的所有者，只更改文件的所属组，注意:不可省略
sudo chown -R #此选项用于变更目录下所有文件及子目录的
sudo chgrp nogroup test.php #将test.php的所属组更改为nogroup，chgrp只有chown的一部分功能，使用不使用完全看个人习惯，同时也支持-R选项

chmod #更改文件的权限
```

---

## 软件包管理
常见的软件包格式有两种，一种是rpm（rpm -ivh），一种是deb（dpkg -i）。高级软件包管理工具有yum（只能用于rpm）和apt
使用**RPM**的linux版本有
SUSE、openSUSE、Red Hat、Fedora、Centos
使用**deb**的linux版本有
Debian、Ubuntu

---

## 磁盘管理
### linxu常见的文件系统
ext3和ext4是linux主流的文件系统，其中ext3是主流，ext4是对ext3的扩展和改善，是更新的文件系统。另一种使用广泛的文件系统是Reiser，曾经是SUSE的默认文件系统。

### 挂载文件系统
```bash
mkdir /mnt/vista
mount /dev/sda3 /mnt/vista/ #将分区挂载到这个目录中
cd /mnt/vista/

mount -r #以只读方式挂载硬盘分区
mount -w #默认值，以可读可写模式挂载设备
mount -t #指定文件系统类型挂载
```

### 启动时候挂载文件系统
```bash
cat /etc/fstab
```

### 查看磁盘使用情况
```bash
df #整理收集当前已经挂载的全部文件系统
df -t ext4 #显示所有已挂载的ext4的文件系统
df -h #最常用这个，使用人类可读的格式
```

### 建立文件系统/格式化
```bash
mkfs -t ext3 /dev/sdb1 #格式化sdb1为ext3格式
```

### 压缩工具
```bash
gzip test.txt #压缩
gzip -d test.txt.gz #解压缩
bzip2 test.tar #压缩
bzip2 -d test.tar.bz2 #解压缩
```

### 存档工具
```bash
tar -cvf shell.tar shell/ #将shell目录连同下所有文件打包成shell.tar
#c表示创建归档文件，v表示显示命令执行过程，f表示指定归档文件的文件名
tar -xvf shell.tar #解压缩shell.tar
tar -cvwf shell.tar shell/ #w表示每次将单个文件归档时征求用户同意

tar -czvf shell.tar.gz shell/ #将shell目录及其下所有文件打包成shell.tar.gz，其中z表示调用gzip压缩归档文件
tar -xzvf shell.tar.gz #与上面相反，z表示调用gzip程序

tar -cjvf #j表示调用bzip2程序
tar -xjvf #j表示调用bzip2程序
```

---

## 用户与用户组管理
```bash
sudo useradd -m john #添加一个用户名为John的用户，并自动创立主目录（-m）
sudo passwd john #更改john的登录密码
sudo useradd -g users mike #在users组中创建用户mike

history #记录用户操作
history 10 #列出最近使用的10条命令

#在linux中，所有的用户信息都等级在/etc/passwd文件中，而/etc/shadow文件则保存着用户的登录密码。其中passwd所有用户可读，shadow只有root用户可读。

sudo userdel mike #删除mike这个账号
sudo userdel -r john #删除john用户同时删除其主目录

usermod
-d 修改用户主目录
-e 修改账号的有效期限
-g 修改用户所属的组
-l 修改用户账号名称
-s 修改用户登录后所使用的shell

id #用于查看用户的UID、GID及其所属的组，以用户名作为参数。
```

---

## 进程管理
```bash
ps aux |grep badpro #查找名为badpro的进程
kill 12974 或 kill -KILL 12974 或 kill -9 12974
#杀死这个进程

top #及时跟踪进程信息，使用Q退出
lsof database.doc #查看正在使用此文件的进程的相关信息

nice #查看进程默认谦让度，一般默认为0，取值-20~19
nice -n 2 bc#设置bc以谦让度增量2启动

ps lax #获得进程的PID以及谦让度，查出bc的PID是8567
renice +12 -p 8567 #将PID为8567的进程bc的谦让度更改为+12
```

---

## 网络配置
```bash
ifconfig #不带参数可以显示当前系统上所有的网络接口配置

sudo ifconfig eth0 203.152.200.113 netmask 255.255.255.0 up
sudo ifconfig eth0 down
#配置IP地址、子网掩码以及其他网络选项。同时可以启动或禁用一个网络接口

netstat -r #查看当前系统中的路由信息
sudo route add default gw 203.152.200.1
sudo route add -net 203.152.200.0/24 gw 203.152.200.1
sudo route add -host 203.152.200.113 gw 203.152.200.1
#以上三条分别是添加默认网关、网络地址段、主机地址的网关配置
sudo route add -host 203.152.200.113 gw 203.152.200.1 dev eth0
#可以对指定的接口配置路由，其中的关键字dev可以省略
sudo route del default #删除默认路由，谨慎使用
```

---
