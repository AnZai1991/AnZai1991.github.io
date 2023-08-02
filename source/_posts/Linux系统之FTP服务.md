---
title: Linux系统之FTP服务
date: 2020-01-20 20:25:22
tags: [Linux,运维]
---

## 环境介绍
操作系统：SUSE Linux
软件版本：vsftp

---

## 操作方法
1、关闭防火墙
```bash
service SuSEfirewall2_setup stop
service SuSEfirewall2_init stop
chkconfig --list |grep firewall #查看防火墙开机启动状态
chkconfig SuSEfirewall2_init off
chkconfig SuSEfirewall2_setup off
```
2、安装FTP
```bash
zypper install -y vsftpd
```
<!--more-->
3、修改主配置文件/etc/vsftpd/vsftpd.conf

```bash
vim /etc/vsftpd.conf
#前面的注释大概意思是说vsftpd.conf的默认编译配置很复杂，看下面的样例会轻松一点，如果需要完整的配置说明，参考vsftpd.conf.5
#如果你不改变任何一下的配置，默认是作为一个开放匿名访问的FTP server

#一般设置
# line 18:取消这个注释，开启任何用户的写权限，默认为YES
write_enable=YES
# line 23:激活目录信息-当登录到某个目录时，显示给远程用户的信息
dirmessage_enable=YES
# line 28:建议为系统设立一个独特的用户作为单独的无权限用户,默认值nobody,应选择一个特定用户
nopriv_user=noboody
# line 32:你可以重新定义登录时候的欢迎语
ftpd_banner="Welcome to FTP service."
# line 39:是否允许递归查询。默认为NO，不允许用户使用"ls -R"命令，这个有个很小的风险。因为一个ls -R如果是在大型站点将会消耗大量资源。
#ls_recurse_enable=YES
# line 44&48:这个选项包含一个文件路径，这个文件中匿名的e-mail地址将会不能登录。这个选项的前提是deny_email_enable可用。对对抗某些DOS攻击有用
#deny_email_enable=YES
#banned_email_file=/etc/vsftpd.banned_emails
# line 53:当设置为YES，在目录列表中所有用户和组信息都会被展示"ftp"
#hide_ids=YES

#本地FTP用户设置
# line 59:取消注释将允许本地用户登录。开启本地用户登录服务。若只允许匿名用户访问，前面加上#注释掉即可阻止本地用户访问FTP服务器
local_enable=YES
# line 64:默认值是077。如果你的本地用户需要创建文件的权限，你可以改变它为022
local_umask=022
# line 69:取消这个注释将会所有本地用户不被限制在他们的主目录,YES为启用 NO禁用.(该项默认值是NO,即在安装vsftpd后不做配置的话，ftp用户是可以向上切换到要目录之外的)
chroot_local_user=NO
# line 75:当chroot_local_user=NO（允许访问主目录以外的目录），chroot_list_enable=YES时候，指定一个黑名单，限制名单里的用户切换到以外的目录。当chroot_local_user=YES（禁止访问主目录以外的目录），chroot_list_enable=YES时候，指定一个白名单，允许表里的用户改变根目录到他们的主目录。YES为启用  NO禁用
chroot_list_enable=YES
# line 79:是否限制在主目录下的用户名单，至于是黑名单还是白名单，这取决于chroot_local_user的值
chroot_list_file=/etc/vsftpd.chroot_list
# line 84:本地用户最大的传输速率。默认值是0（不限制）
local_max_rate=7200

#匿名FTP用户设置
# line 91:是否允许匿名登录FTP服务器，默认设置为YES允许
anonymous_enable=NO
# line 96:匿名用户将只被允许下载全局可读文件
anon_world_readable_only=YES
# line 102:取消此注释将允许匿名FTP用户上传文件。只有全局的写权限激活才能起作用。当然，你需要创建一个可供写入的目录为匿名FTP用户
anon_upload_enable=NO
# line 107:匿名用户默认的umas是077，如果你需要给匿名用户创建文件的权限，可以改为022
#anon_umask=022
# line 112:取消这个注释，将允许匿名用户创建一个新的目录。如果设置YES。在特定的条件下匿名用户可以被允许创建一个新的目录。为了这个选项有效，选项write_enable必须激活（也就是write_enable是前提）。匿名ftp用户必须有父目录具有写的权限。
anon_mkdir_write_enable=NO
# line 117:取消这个注释，匿名用户将被允许获得其他的写权限，列如删除和重命名。如果设置为YES，匿名用户将会被允许写的操作除了上传和创建目录，例如删除和重命名，考虑完整性，这个一般不推荐使用。
#anon_other_write_enable=YES
# line 123&124:如果你需要，你可以安排上传的匿名文件归其他的某个用户所有。注意，用root上传文件是不推荐的。
#chown_uploads=YES
#chown_username=whoever
# line 129:匿名用户的传输速率，默认为0（不限制）
#anon_max_rate=7200

#日志设置
# line 136:使用系统日志守护进程记录日志，而不是日志文件
syslog_enable=NO
# line 140:取消这个注释将记录所有的FTP请求和响应
log_ftp_protocol=NO
# line 144:激活日志记录详细的上传和下载
xferlog_enable=YES
# line 149:设置vsftpd日志的路径
vsftpd_log_file=/var/log/vsftpd.log
# line 154:你可以设置你的日志文件为标准的ftp xferlog格式。注意：这将禁止正常的日志记录，除非你开启了下面的dual_log_enable
#xferlog_std_format=YES
# line 159:设置xferlog_file日志的路径
#xferlog_file=/var/log/xferlog
# line 163:同时记录两种日志，标准xferlog和vsftpd自己的日志
#dual_log_enable=YES
# line 167:取消这个注释，将使你可以看到vsftp系统监听进程的工作状态信息，出于安全考虑禁用此选项
#setproctitle_enable=YES

#传输设置
# line 173:确保端口传输连接通过常规的20端口
connect_from_port_20=YES
# line 177:设置客户端两个命令之前超时的时间，单位秒
idle_session_timeout=600
# line 181:设置数据传输过程中暂停/延迟最长时间，超时被触发，客户端将会被踢掉。
data_connection_timeout=120
# line 187:开启这个功能，服务将能识别到异步ABOR响应。出于安全不建议开启，然而关闭的话，有些老的FTP客户端可能会不识别。
#async_abor_enable=YES
# line 200:是否以ASCII方式传输数据。默认情况下，服务器会忽略ASCII方式的请求。
ascii_upload_enable=YES
ascii_download_enable=YES
# line 206:设置为NO时候，不允许采用PASV（被动方法）去获取数据连接
#pasv_enable=NO
# line 210:PAM设置。别改变它除非你知道你在做什么。对应PAM服务器名称将会被vsftpd使用
pam_service_name=vsftpd
# line 213:当设置为YES，vsftpd将会被OpenSSL反编译。vsftpd将会通过SSL支持安全连接。这个应用控制连接（包含登录）和数据连接。你将需要客户端支持SSL。注意：小心开启此功能。只要确保你需要它vsftpd将会不能保证OpenSSL库的安全。通过开启这个选项，你在声明你将信任安装的OpenSSL库。
ssl_enable=NO
# add at the bottom: specify  your root directory
local_root=/home
#当设置为YES，vsf将会用本地时间展示目录列表。这个默认展示GMT。这个时间通过MDTM FTP命令返回，它也会影响到这选项。
use_localtime=YES
#当一个匿名用户登录之后，vsftpd将会尝试接入当前这个选项代表一个目录下，如果失败将会忽略
anon_root=none
#如果vsftpd运行单独模式，这个最大客户端数量可以被连接。任何其他多余连接将会报错。m默认是0（没有限制）
max_clients=0
#如果vsftpd运行单独模式，这个最大数量客户端，它连接相同资源网络地址。另外多余客户端再连接将会抛出错误。默认是0（没有限制）
max_per_ip=0
#当设置为NO，你不允许采用PASV（被动方法）去获取数据连接。
pasv_enable=YES
#允许最大端口分配PASV方式数据连接。你可以定义一个窄的端口范围允许通过防火墙
pasv_max_port=40500
#允许最小端口分配PASV方式数据连接。你可以定义一个端口范围允许通过防火墙
pasv_min_port=40000
#对于本地验证用户，最大传输速率被允许，单位字节/秒。
local_max_rate=500000
```
vsftpd.conf.5的详细的中文配置文件可以参阅：https://blog.csdn.net/m0_37355951/article/details/76974622
4、重启
```bash
service vsftpd start #启动FTP服务
service vsftpd stop #关闭FTP服务
service vsftpd restart #重启FTP服务
chkconfig vsftpd on #开机启动
chkconfig vsftpd off #开机不启动
```
5、vsftpd的其他配置文件
|  文件路径   | 说明  |
|  ----  | ----  |
| /usr/sbin/vsftpd  | Vsftpd的主程序 |
| /etc/rc.d/init.d/vsftpd  | 启动脚本 |
| /etc/pam.d/vsftpd  | PAM认证文件（此文件中file=/etc/vsftpd/ftpusers字段，指明阻止访问的用户来自/etc/vsftpd/ftpusers文件中的用户） |
| /etc/vsftpd/ftpusers  | 禁止使用vsftpd的用户列表文件。记录不允许访问FTP服务器的用户名单，管理员可以把一些对系统安全有威胁的用户账号记录在此文件中，以免用户从FTP登录后获得大于上传下载操作的权利，而对系统造成损坏。（注意：linux-4中此文件在/etc/目录下） |
| /etc/vsftpd/user_list  | 禁止或允许使用vsftpd的用户列表文件。这个文件中指定的用户缺省情况（即在/etc/vsftpd/vsftpd.conf中设置userlist_deny=YES）下也不能访问FTP服务器，在设置了userlist_deny=NO时,仅允许user_list中指定的用户访问FTP服务器。（注意：linux-4中此文件在/etc/目录下） |
| /var/ftp  | 匿名用户主目录；本地用户主目录为：/home/用户主目录，即登录后进入自己家目录 |
| /var/ftp/pub  | 匿名用户的下载目录，此目录需赋权根chmod 1777 pub（1为特殊权限，使上载后无法删除） |
| /etc/logrotate.d/vsftpd.log  | Vsftpd的日志文件 |