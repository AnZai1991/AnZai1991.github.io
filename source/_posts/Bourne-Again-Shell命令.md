---
title: Bourne Again Shell命令
date: 2020-10-19 18:36:44
tags: [操作系统,Linux,Shell]

---

## 一、linux基础指令
### 1、文件与目录操作
1.1  ls指令：显示目录内容
1.2  cd指令：将当前工作目录切换到指定目录
1.3  cp指令：复制文件或目录
1.4  mv指令：移动文件或改名
1.5  pwd指令：打印当前工作目录
1.6  rm指令：删除文件或目录
1.7  rmdir指令：删除空目录
1.8  chgrp指令：改变文件所属工作组
1.9  chmod指令：改变文件访问权限
1.10  chown指令：改变文件的所有者和所属工作组
1.11  find指令：查找文件并执行指定的操作
<!--more-->	
1.12  ln指令：为文件创建连接
1.13  mkdir指令：创建目录
1.14  whereis指令：显示指令及相关文件的路径
1.15  which指令：显示指令的绝对路径
1.16  file指令：探测文件类型
1.17  touch指令：设置文件的时间属性
1.18  locate/slocate指令：快速定位文件的路径
1.19  dd指令：复制文件并进行内容转换
1.20  updatedb指令：创建或更新slocate数据库
1.21  dirname指令：去除文件名中的非目录部分
1.22  pathchk指令：检查文件路径名的有效性和可移植性
1.23  unlink指令：调用unlink系统调用删除指定文件
1.24  basename指令：去掉文件名中的路径和后缀
1.25  rename指令：批量为文件改名
##### 查看目录和文件
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

##### 列出目录内容
```bash
dir   #与Windows系统一样
vdir   #相当于ls -l
```

##### 查找文件
```bash
find /etc/ -name init.d -type d -print   #跟路径，跟名称，跟类型，打印
find /usr/bin/ -type f -atime +100 -print   #跟路径，跟类型，最近一次修改在100天前的文件（100天内没修改过的文件），跟打印
find . -type f -mtime -1 -print   #跟路径，跟类型，跟最近一天内修改的文件
```

##### 更快的定位文件
```bash
locate *.doc   #检索文件名数据库，需要更新的话使用root权限执行updatedb
```

##### 查找特定程序
```bash
whereis find   #返回这个命令的二进制可执行文件位置、源代码文件和使用手册位置
whereis -b find   #只返回可执行文件位置
```

##### 创建目录和文件
```bash
mkdir   #建立目录
mkdir -p ~/temp/job   #加-p可以直接创建完整的目录，先创建temp再创建job
touch #创建空白文件。也有更新文件建立日期和时间的功能
```

##### 移动、复制和删除
```bash
mv hello bin/
mv photos/ 桌面/ #既可以移动文件也可以移动目录，同时对其重命名！
mv -i #此选项用于防止直接覆盖文件，若有同名文件或文件夹，将提示是否覆盖
mv -b #此选项同样是解决同名覆盖的问题，会在同名的文件后加上~
```

##### 复制文件和目录
```bash
cp test.php test/
cp -i #与mv相同，为了防止同名覆盖问题
cp -b #与mv相同，为了解决同名覆盖，会在文件或目录后加上~

cp test/ 桌面/ #这会将test目录里的内容复制到桌面路径下，但是不会复制test目录本身
cp -r test/ 桌面/ #此选项会将子目录及其内容一起复制到另一个子目录下
```

##### 删除目录和文件
```bash
rmdir #跟目录名，删除此目录，且只能删除空白目录
rm test/*.php #删除test目录下所有的php文件
rm -i #此选项用于逐个确认是否删除，对于只读文件，加不加-i都会提示是否删除

rm -f #避免提示，默认全部同意删除
rm -r #删除目录下所有的文件和子目录
rm -rf #谨慎使用，有一个更安全的命令delete

delete #
```

##### 改变文件所有权
```bash
sudo chown ayb:root test.php #将test.php的文件所有者改为ayb，所属组改为root组
sudo chown :root test.php #保留test.php的所有者，只更改文件的所属组，注意:不可省略
sudo chown -R #此选项用于变更目录下所有文件及子目录的
sudo chgrp nogroup test.php #将test.php的所属组更改为nogroup，chgrp只有chown的一部分功能，使用不使用完全看个人习惯，同时也支持-R选项

chmod #更改文件的权限
```

### 2、文本编辑
2.1  vi指令：全屏幕纯文本编辑器
2.2  emacs指令：全屏文本编辑器
2.3  ed指令：行文本编辑器
2.4  ex指令：以Ex模式运行vi指令
2.5  jed指令：程序员的文本编辑器
2.6  pico指令：文本编辑器
2.7  sed指令：用于文本过滤和转换的流式编辑器
2.8  joe指令：全屏文本编辑器

### 3、文本过滤与处理
3.1  cat指令：连接文件并显示内容
3.2  more指令：文件内容分屏查看器
3.3  less指令：分屏显示文件内容
3.4  grep指令：在文件中搜索匹配的行
3.5  head指令：显示文件的头部内容
3.6  tail指令：输出文件尾部内容
3.7  wc指令：统计文件的字节数、单词数和行数
3.8  uniq指令：报告或忽略文件中的重复行
3.9  cut指令：删除文件中的指定字段
3.10  sort指令：对文件进行行排序
3.11  join指令：将两个文件的相同字段合并
3.12  split指令：将文件分割成碎片
3.13  unexpand指令：将空白（space）转换为制表符
3.14  tr指令：转换和删除字符
3.15  tee指令：将输入内容复制到标准输出或文件
3.16  tac指令：以行为单位反序连接和打印文件
3.17  spell指令：拼写检查
3.18  paste指令：合并文件
3.19  diff指令：比较两个文件的不同
3.20  cmp指令：比较两个文件
3.21  look指令：显示文件中以指定字符串开头的行
3.22  ispell指令：拼写检查程序
3.23  fold指令：指定文件显示的宽度
3.24  fmt指令：优化文本格式
3.25  expand指令：将制表符转换为空白字符
3.26  col指令：具有反向换行的文本过滤器
3.27  colrm指令：删除文件中的指定列
3.28  comm指令：以行为单位比较两个已排序文件
3.29  csplit指令：将文件分割为若干小文件
3.30  diff3指令：比较3个文件的不同
3.31  diffstat指令：显示diff输出的柱状图
3.32  printf指令：格式化并打印数据
3.33  pr指令：将文本转换为适合打印格式
3.34  od指令：将文件导出为八进制或其他格式
3.35  rev指令：将文件的每行内容以字符为单位反序输出

##### 查看文本文件
```bash
cat   #查看文件内容，通常是一个文本文件，也可以跟多个文件名作为参数。当然也可以用通配符
cat -n   #在查看的同时每一行前显示行号
more   #不会直接显示所有的内容，空格键翻动一页，按Enter向下滚动一行，Q键退出。
```

##### 阅读文件的开头和结尾
```bash
head -n   #加上数字参数和文件名参数，显示此文件的头几行
tail -n   #加上数字参数和文件名参数，显示此文件的后几行
```

##### 更好的文本阅读工具
```bash
less   #空格键下一页，B键上一页，Q键退出，"/"后跟需要查找的内容
less -M   #底部输出额外的文件阅读信息
```

##### 查找文件内容
```bash
grep [options] PATTERN [file]   #显示文件中包含某字符的行，如果要查询关键词需要加上单引号
egrep   #同grep一样
```

### 4、备份压缩
4.1  tar指令：打包备份
4.2  gzip指令：GNU的压缩与解压缩工具
4.3  gunzip指令：解压缩.gz压缩包
4.4  bzip2指令：创建和管理.bz2压缩包
4.5  bunzip2指令：解压缩.bz2压缩包
4.6  cpio指令：存取归档包中的文件
4.7  dump指令：ext2/3文件备份工具
4.8  restore指令：还原dump备份
4.9  compress指令：压缩文件
4.10  uncompress指令：解压缩.Z压缩包
4.11  zip指令：压缩和文件打包工具
4.12  unzip指令：解压缩.zip压缩包
4.13  arj指令：.arj压缩包管理器
4.14  unarj指令：解压缩.arj压缩包
4.15  bzcat指令：显示.bz2压缩包中的文件内容
4.16  bzcmp指令：比较.bz2压缩包中的文件
4.17  bzdiff指令：比较两个.bz2压缩包中文件的不同
4.18  bzgrep指令：搜索.bz2压缩包中文件的内容
4.19  bzip2recover指令：恢复被破坏的.bz2压缩包中的文件
4.20  bzmore指令：分屏查看.bz2压缩包中的文本文件
4.21  bzless指令：增强的.bz2压缩包分屏查看器
4.22  zipinfo指令：显示zip压缩包的细节信息
4.23  zipsplit指令：分割zip压缩包
4.24  zforce指令：强制gzip格式文件的后缀为.gz
4.25  znew指令：将.Z文件重新压缩为.gz文件
4.26  zcat指令：显示.gz压缩包中文件的内容
4.27  gzexe指令：压缩可执行文件

##### 压缩工具
```bash
gzip test.txt #压缩
gzip -d test.txt.gz #解压缩
bzip2 test.tar #压缩
bzip2 -d test.tar.bz2 #解压缩
```

##### 存档工具
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

### 5、shell内部指令
5.1  echo指令：打印变量或字符串
5.2  kill指令：杀死进程
5.3  alias指令：设置命令别名
5.4  unalias指令：取消命令别名
5.5  jobs指令：显示任务列表
5.6  bg指令：后台执行作业
5.7  fg指令：将后台作业放到前台执行
5.8  set指令：显示或设置shell特性及shell变量
5.9  unset指令：删除指定的shell变量与函数
5.10  env指令：在定义的环境中执行指令
5.11  type指令：判断内部指令和外部指令
5.12  logout指令：退出登录
5.13  exit指令：退出shell
5.14  export指令：将变量输出为环境变量
5.15  wait指令：等待进程执行完后返回终端
5.16  history指令：显示历史命令
5.17  read指令：从键盘读取变量值
5.18  enable指令：激活或关闭内部命令
5.19  exec指令：调用并执行指令
5.20  ulimit指令：限制用户对shell资源的使用
5.21  umask指令：设置权限掩码
5.22  shopt指令：显示和设置shell行为选项
5.23  help指令：显示内部命令的帮助信息
5.24  bind指令：命令行键序列功能绑定设置
5.25  builtin指令：执行shell内部命令
5.26  command指令：调用指定的指令并执行
5.27  declare指令：声明shell变量
5.28  dirs指令：显示目录堆栈
5.29  pushd指令：向目录堆栈中压入目录
5.30  popd指令：从目录堆栈中弹出目录
5.31  readonly指令：定义只读shell变量或函数
5.32  fc指令：修改历史命令并执行

##### 操作记录
```bash
history #记录用户操作
history 10 #列出最近使用的10条命令
```

### 6、系统关机
6.1  ctrlaltdel指令：设置Ctrl+Alt+Del 组合键的功能
6.2  halt指令：关闭计算机
6.3  poweroff指令：关闭计算机并切断电源
6.4  reboot指令：重新启动计算机
6.5  shutdown指令：关闭计算机

### 7、打印相关
7.1  lp指令：打印文件
7.2  lpr指令：打印文件
7.3  lprm指令：删除打印任务
7.4  lpc指令：打印机控制程序
7.5  lpq指令：显示打印队列状态
7.6  lpstat指令：显示CUPS的状态信息
7.7  accept指令：接受打印任务
7.8  reject指令：拒绝打印任务
7.9  cancel指令：取消打印任务
7.10  cupsdisable指令：停止打印机
7.11  cupsenable指令：启动打印机
7.12  lpadmin指令：管理CUPS打印机

### 8、实用工具
8.1  man指令：帮助手册
8.2  info指令：GNU格式在线帮助
8.3  cksum指令：计算文件的校验和与统计文件字节数
8.4  bc指令：任意精度的计算器语言
8.5  cal指令：显示日历
8.6  sum指令：打印文件的校验和
8.7  md5sum指令：计算和检查文件的md5报文摘要
8.8  hostid指令：打印当前主机数字标识
8.9  date指令：显示与设置系统日期时间
8.10  dircolors指令：ls指令显示颜色设置
8.11  gpm指令：虚拟控制台下的鼠标工具
8.12  sleep指令：暂停指定的时间
8.13  whatis指令：从数据库中查询指定的关键字
8.14  who指令：打印当前登录用户
8.15  whoami指令：打印当前用户名
8.16  wall指令：向所有终端发送信息
8.17  write指令：向指定用户终端发送信息
8.18  mesg指令：控制终端是否可写
8.19  talk指令：用户聊天客户端工具
8.20  login指令：登录指令
8.21  mtools指令：DOS兼容工具集
8.22  stty指令：修改终端命令行设置
8.23  tee指令：双向重定向指令
8.24  users指令：打印登录系统的用户
8.25  clear指令：清屏指令
8.26  consoletype指令：打印已连接的终端类型
8.27  sln指令：静态ln
8.28  yes指令：重复打印字符串直到被杀死

##### 寻求帮助
```bash
man   #跟命令，获取命令和系统调用帮助手册
whatis   #跟命令，返回命令简介
apropos   #跟关键词，用来查找相关的命令，跟whatis作用相反
```

---

## 二、Linux系统管理命令
### 9、用户和工作组管理
9.1  useradd指令：创建新用户
9.2  userdel指令：删除用户及相关文件
9.3  passwd指令：设置用户密码
9.4  groupadd指令：创建新工作组
9.5  groupdel指令：删除工作组
9.6  su指令：切换用户身份
9.7  usermod指令：修改用户
9.8  chfn指令：改变用户finger信息
9.9  chsh指令：改变用户的登录shell
9.10  finger指令：查询用户信息
9.11  gpasswd指令：工作组文件管理工具
9.12  groupmod指令：修改工作组信息
9.13  groups指令：打印用户所属工作组
9.14  pwck指令：验证密码文件完整性
9.15  grpck指令：验证组文件的完整性
9.16  logname指令：打印当前用户的登录名
9.17  newusers指令：批处理创建用户
9.18  chpasswd指令：以批处理模式更新密码
9.19  nologin指令：礼貌地拒绝用户登录
9.20  pwconv指令：创建用户影子文件
9.21  pwunconv指令：还原用户密码到passwd文件
9.22  grpconv指令：创建组影子文件
9.23  grpunconv指令：还原组密码到group文件

##### 用户与用户组管理
```bash
sudo useradd -m john #添加一个用户名为John的用户，并自动创立主目录（-m）
sudo passwd john #更改john的登录密码
sudo useradd -g users mike #在users组中创建用户mike
#在linux中，所有的用户信息都登记在/etc/passwd文件中，而/etc/shadow文件则保存着用户的登录密码。其中passwd所有用户可读，shadow只有root用户可读。

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

### 10、硬件相关
10.1  arch指令：打印主机架构类型
10.2  cdrecord指令：光盘刻录工具
10.3  eject指令：弹出可移动媒体
10.4  volname指令：显示卷名
10.5  lsusb指令：显示USB设备列表
10.6  lspci指令：显示PCI设备列表
10.7  setpci指令：配置PCI设备
10.8  hwclock指令：查询与设置硬件时钟
10.9  systool指令：查看系统设备信息

### 11、磁盘管理
11.1  df指令：报告磁盘空间使用情况
11.2  fdisk指令：Linux下的硬盘分区工具
11.3  parted指令：强大的硬盘分区工具
11.4  mkfs指令：创建文件系统
11.5  badblocks指令：查找磁盘坏块
11.6  partprobe指令：确认分区表的改变
11.7  convertquota指令：转换老的磁盘配额数据文件
11.8  grub指令：多重引导程序grub的shell工具
11.9  lilo指令：Linux引导加载器
11.10  mkbootdisk指令：创建引导软盘
11.11  hdparm指令：读取并设置硬盘参数
11.12  mkinitrd指令：为预加载模块创建初始化RAM磁盘映像
11.13  mkisofs指令：创建光盘映像文件
11.14  mknod指令：创建字符或者块设备文件
11.15  mkswap指令：创建交换分区或者交换文件
11.16  blockdev指令：命令行中调用磁盘的Ioctl
11.17  pvcreate指令：创建物理卷
11.18  pvscan指令：扫描所有磁盘上的物理卷
11.19  pvdisplay指令：显示物理卷属性
11.20  pvremove指令：删除指定物理卷
11.21  pvck指令：检查物理卷元数据
11.22  pvchange指令：修改物理卷属性
11.23  pvs指令：输出物理卷信息报表
11.24  vgcreate指令：创建卷组
11.25  vgscan指令：扫描并显示系统中的卷组
11.26  vgdisplay指令：显示卷组属性
11.27  vgextend指令：向卷组中添加物理卷
11.28  vgreduce指令：从卷组中删除物理卷
11.29  vgchange指令：修改卷组属性
11.30  vgremove指令：删除卷组
11.31  vgconvert指令：转换卷组元数据格式
11.32  lvcreate指令：创建逻辑卷
11.33  lvscan指令：扫描逻辑卷
11.34  lvdisplay指令：显示逻辑卷属性
11.35  lvextend指令：扩展逻辑卷空间
11.36  lvreduce指令：收缩逻辑卷空间
11.37  lvremove指令：删除逻辑卷
11.38  lvresize指令：调整逻辑卷空间大小

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

### 12、文件系统管理
12.1  mount指令：加载文件系统
12.2  umount指令：卸载文件系统
12.3  mkfs指令：创建文件系统
12.4  mke2fs指令：创建ext2/ext3文件系统
12.5  fsck指令：检查文件系统
12.6  dumpe2fs指令：导出ext2/ext3文件系统信息
12.7  e2fsck指令：检查ext2/ext3文件系统
12.8  chattr指令：改变文件的第二扩展文件系统属性
12.9  lsattr指令：查看文件的第二扩展文件系统属性
12.10  mountpoint指令：判断目录是否是加载点
12.11  edquota指令：编辑磁盘配额
12.12  quotacheck指令：磁盘配额检查
12.13  quotaoff指令：关闭磁盘配额功能
12.14  quotaon指令：激活磁盘配额功能
12.15  quota指令：显示用户磁盘配额
12.16  quotastats指令：查询磁盘配额运行状态
12.17  repquota指令：打印磁盘配额报表
12.18  swapoff指令：关闭交换空间
12.19  swapon指令：激活交换空间
12.20  sync指令：刷新文件系统缓冲区
12.21  e2image指令：将ext2/ext3文件元数据保存到文件
12.22  e2label指令：设置文件系统卷标
12.23  tune2fs指令：调整ext2/ext3文件系统参数
12.24  resize2fs指令：调整ext2文件系统大小
12.25  stat指令：显示文件状态
12.26  findfs指令：通过卷标或UUID查找文件系统

##### linxu常见的文件系统
ext3和ext4是linux主流的文件系统，其中ext3是主流，ext4是对ext3的扩展和改善，是更新的文件系统。另一种使用广泛的文件系统是Reiser，曾经是SUSE的默认文件系统。

##### 挂载文件系统
```bash
mkdir /mnt/vista
mount /dev/sda3 /mnt/vista/ #将分区挂载到这个目录中
cd /mnt/vista/

mount -r #以只读方式挂载硬盘分区
mount -w #默认值，以可读可写模式挂载设备
mount -t #指定文件系统类型挂载
```

##### 启动时候挂载文件系统
```bash
cat /etc/fstab
```

### 13、进程与作业管理
13.1  at指令：在指定时间执行任务
13.2  atq指令：显示用户待执行任务列表
13.3  atrm指令：删除待执行任务
13.4  batch指令：在指定时间执行任务
13.5  crontab指令：周期性执行的任务
13.6  init指令：初始化Linux进程
13.7  killall指令：按照名称杀死进程
13.8  nice指令：以指定优先级运行程序
13.9  nohup指令：以忽略挂起信号方式运行程序
13.10  pkill指令：按名称杀死进程
13.11  pstree指令：以树形显示进程派生关系
13.12  ps指令：报告系统当前进程快照
13.13  renice指令：调整进程优先级
13.14  skill指令：向进程发送信号
13.15  watch指令：全屏方式显示周期性执行的指令
13.16  w指令：显示已登录用户正在执行的指令
13.17  telinit指令：切换运行等级
13.18  runlevel指令：打印当前运行等级
13.19  service指令：控制系统服务
13.20  ipcs指令：报告进程间通信设施状态
13.21  pgrep指令：基于名称查找进程
13.22  pidof指令：查找进程ID号
13.23  pmap指令：报告进程的内存映射

##### 进程管理
```bash
ps aux |grep badpro #查找名为badpro的进程
kill 12974 或 kill -KILL 12974 或 kill -9 12974
#杀死这个进程

nice #查看进程默认谦让度，一般默认为0，取值-20~19
nice -n 2 bc#设置bc以谦让度增量2启动

ps lax #获得进程的PID以及谦让度，查出bc的PID是8567
renice +12 -p 8567 #将PID为8567的进程bc的谦让度更改为+12
```

### 14、性能监测与优化
14.1  top指令：实时报告系统整体性能情况
14.2  uptime指令：报告系统运行时长及平均负载
14.3  free指令：显示内存的使用情况
14.4  iostat指令：报告CPU状态和设备及分区的I/O状态
14.5  mpstat指令：报告CPU相关状态
14.6  sar指令：搜集、报告和保存系统活动状态
14.7  vmstat指令：报告系统整体运行状态
14.8  time指令：统计指令运行时间
14.9  tload：图形化显示系统平均负载
14.10  lsof指令：显示所有已打开文件列表
14.11  fuser指令：报告进程使用的文件或套接字
14.12  cpuspeed指令：用户空间CPU频率控制程序

##### 性能检测
```bash
top #及时跟踪进程信息，使用Q退出
lsof database.doc #查看正在使用此文件的进程的相关信息
```

### 15、内核与模块
15.1  sysctl指令：运行时配置内核参数
15.2  lsmod指令：显示已加载模块状态
15.3  insmod指令：加载模块到内核
15.4  modprobe指令：内核模块智能加载工具
15.5  rmmod指令：从内核中移除模块
15.6  bmodinfo指令：显示模块详细信息
15.7  depmod指令：产生模块依赖的映射文件
15.8  uname指令：打印系统信息
15.9  dmesg指令：打印和控制内核环形缓冲区
15.10  kexec指令：直接启动另一Linux内核
15.11  get_module指令：获取模块信息
15.12  kernelversion指令：打印内核主版本号
15.13  slabtop指令：实时显示内核slab缓冲区信息

### 16、X-Window系统
16.1  startx指令：初始化X-Window会话
16.2  xauth指令：修改X服务器访问授权信息
16.3  xhost指令：X服务器访问控制工具
16.4  xinit指令：X-Window系统初始化程序
16.5  xlsatoms指令：显示X服务器定义的原子成分
16.6  xlsclients指令：列出在Ｘ服务器上显示的客户端程序
16.7  xlsfonts指令：显示X服务器字体列表
16.8  xset指令：X-Window系统的用户爱好设置

### 17、软件包管理
17.1  rpm指令：RPM软件包管理器
17.2  yum指令：基于RPM的软件包管理器
17.3  chkconfig指令：管理不同运行等级下的服务
17.4  ntsysv指令：配置不运行等级下的服务
17.5  apt-get指令：APT包管理工具
17.6  aptitude指令：基于文本界面的软件包管理工具
17.7  apt-key指令：管理APT软件包的密钥
17.8  apt-sortpkgs指令：排序软件包索引文件
17.9  dpkg指令：Debian包管理器
17.10  dpkg-deb指令：Debian包管理器
17.11  dpkg-divert指令：将文件安装到转移目录
17.12  dpkg-preconfigure指令：软件包安装前询问问题
17.13  dpkg-query指令：在dpkg数据库中查询软件包
17.14  dpkg-reconfigure指令：重新配置已安装的软件包
17.15  dpkg-split指令：分割软件包
17.16  dpkg-statoverride指令：改写所有权和模式
17.17  dpkg-trigger指令：软件包触发器
17.18  patch指令：为代码打补丁
17.19  rcconf指令：Debian运行等级服务配置工具
17.20  rpm2cpio指令：将RPM包转换为cipo文件
17.21  rpmbuild指令：创建RPM软件包
17.22  rpmdb指令：RPM数据库管理工具
17.23  rpmquery指令：RPM软件包查询工具
17.24  rpmsign指令：管理RPM软件包签名
17.25  rpmverify指令：验证RPM包

##### 从终端运行程序
```bash
dpkg -l |grep chrome
google-chrome-stable   #终端挂起，运行chrome浏览器，直到被关闭
google-chrome-stable &   #在后台运行，终端继续等待接收用户输入，并提示进程号
```
常见的软件包格式有两种，一种是rpm（rpm -ivh），一种是deb（dpkg -i）。高级软件包管理工具有yum（只能用于rpm）和apt
使用**RPM**的linux版本有
SUSE、openSUSE、Red Hat、Fedora、Centos
使用**deb**的linux版本有
Debian、Ubuntu

### 18、系统安全
18.1  chroot指令：切换根目录环境
18.2  lastb指令：显示错误登录列表
18.3  last指令：显示用户最近登录列表
18.4  lastlog指令：显示用户最近一次登录信息
18.5  logsave指令：将指令输出信息保存到日志
18.6  logwatch指令：分析报告系统日志
18.7  logrotate指令：日志轮转工具
18.8  sudo指令：以另一个用户身份执行指令

### 19、编程开发
19.1  test指令：测试条件表达式
19.2  expr指令：表达式求值
19.3  gcc指令：GNU C/C++编译器
19.4  gdb指令：GNU调试器
19.5  ld指令：GNU连接器
19.6  ldd指令：打印程序依赖的共享库
19.7  make指令：GNU工程化编译工具
19.8  as指令：GNU汇编器
19.9  gcov指令：测试代码覆盖率
19.10  nm指令：显示目标文件符号表
19.11  perl指令：perl语言解释器
19.12  php指令：PHP的命令行接口
19.13  protoize指令：添加函数原型
19.14  unprotoize指令：删除函数原型
19.15  mktemp指令：创建临时文件

---

## 三、Linux网络管理指令
### 20、网络配置
20.1  ifconfig指令：配置网络接口
20.2  route指令：显示并设置路由
20.3  ifcfg指令：配置网络接口
20.4  ifdown指令：禁用网络接口
20.5  ifup指令：激活网络接口
20.6  hostname指令：显示和设置系统的主机名称
20.7  dhclient指令：动态主机配置协议客户端工具
20.8  dnsdomainname指令：打印DNS的域名
20.9  domainname指令：显示和设置系统的NIS域名
20.10  nisdomainname指令：显示NIS域名
20.11  usernetctl指令：授权用户操纵网络接口
20.12  ypdomainname指令：显示NIS域名

##### 网络配置
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

### 21、网络测试
21.1  ping指令：测试主机之间网络连通性
21.2  netstat指令：显示网络状态
21.3  nslookup指令：域名查询工具
21.4  traceroute指令：追踪报文到达目的主机的路由
21.5  arp指令：操纵arp缓冲区
21.6  dig指令：DNS查询工具
21.7  host指令：域名查询工具
21.8  nc/netcat指令：随意的操纵TCP或UDP连接和监听端口
21.9  arping指令：发送ARP请求报文给邻居主机
21.10  arpwatch指令：监控arp缓冲区的变化
21.11  tracepath指令：追踪报文经过的路由信息

### 22、网络应用
22.1  elinks指令：纯文本界面的WWW浏览器
22.2  elm指令：E-mail客户端程序
22.3  ftp指令：文件传输协议客户端
22.4  ipcalc指令：简单的IP地址计算器
22.5  lftp指令：文件传输程序
22.6  lftpget指令：使用lftp下载文件
22.7  lynx指令：纯文本网页浏览器
22.8  mailq指令：打印邮件传输队列
22.9  mailstat指令：显示到达的邮件状态
22.10  mail指令：接收和发送电子邮件
22.11  rcp指令：远程文件复制
22.12  rlogin指令：远程登录
22.13  rsh指令：远程shell
22.14  resec指令：远程执行指令客户端
22.15  telnet指令：远程登录工具
22.16  tftp指令：简单文件传输协议客户端
22.17  wget指令：从指定URL地址下载文件
22.18  ncftp指令：增强FTP客户端工具

### 23、高级网络指令
23.1  iptables指令：内核包过滤与NAT管理工具
23.2  iptables-save指令：保存iptables表
23.3  iptables-restore指令：还原iptables表
23.4  ip6tables指令：ipv6版内核包过滤管理工具
23.5  ip6tables-save指令：保存ip6tables表
23.6  ip6tables-restore指令：还原ip6tables表
23.7  ip指令：显示或操纵路由、网络设备和隧道
23.8  tcpdump指令：监听网络流量
23.9  arpd指令：ARP协议守护进程
23.10  arptables指令：ARP包过滤管理工具
23.11  lnstat指令：显示Linux的网络状态
23.12  nstat /rtacct指令：网络状态统计工具
23.13  ss指令：显示活动套接字连接
23.14  iptraf指令：监视网卡流量

### 24、网络服务器
24.1  ab指令：Apache的Web服务器基准测试程序
24.2  apachectl指令：Apache Web服务器控制接口
24.3  exportfs指令：输出NFS文件系统
24.4  ftpcount指令：显示proftpd服务器当前连接用户数
24.5  ftpshut指令：在指定时间停止Proftpd服务
24.6  ftptop指令：显示proftpd服务器连接状态
24.7  ftpwho指令：显示当前每个ftp会话信息
24.8  htdigest指令：管理用户摘要认证文件
24.9  htpasswd指令：管理用户基本认证文件
24.10  httpd指令：Apache的Web服务器守护进程
24.11  mailq指令：打印邮件发送队列
24.12   mysqldump指令：MySQL数据库备份工具
24.13  mysqladmin指令：MySQL服务器的客户端管理工具
24.14  mysqlimport指令：MySQL服务器的数据导入工具
24.15  mysqlshow指令：显示数据库、数据表和列信息
24.16  mysql指令：MySQL服务器的客户端工具
24.17  nfsstat指令：列出NFS状态
24.18  sendmail指令：电子邮件传送代理
24.19  showmount指令：显示NFS服务器的加载信息
24.20  smbclient指令：samba套件的客户端工具
24.21  smbpasswd指令：修改用户SMB密码
24.22  squidclient指令：squid客户端管理工具
24.23  squid指令：代理服务器守护进程

### 25、网络安全
25.1  scp指令：安全远程文件复制
25.2  sftp指令：加密文件传输
25.3  ssh指令：安全连接客户端
25.4  sshd指令：openssh服务器守护进程
25.5  ssh-keygen指令：生成、管理和转换认证密钥
25.6  ssh-keyscan指令：收集主机的ssh公钥
25.7  sftp-server指令：安全FTP服务器
25.8  iptstate指令：以top风格显示内核的iptables状态
25.9  nmap指令：网络探测工具和安全/端口扫描器

---
