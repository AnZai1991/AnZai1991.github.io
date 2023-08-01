---
title: Linux系统之Logrotate
date: 2023-08-01 13:05:35
tags: [Linux,运维]
---

## 环境介绍

操作系统：CentOS Linux release 7.6.1810 (Core)
软件版本：logrotate 3.8.6

## logrotate简介

logrotate是linux默认自带的日志工具，它可以自动对日志进行截断（或轮循）、压缩以及删除旧的日志文件
/usr/sbin/logrotate        #程序所在位置；
/etc/cron.daily/logrotate    #默认让cron每天执行logrotate一次；
/etc/logrotate.conf        #全局配置文件；(通常不需要对它进行修改)
/etc/logrotate.d/          #应用自个的配置文件存放目录，覆盖全局配置；(日志文件独立的配置文件，它（们）放在此目录中)
<!--more-->
```bash
logrotate [OPTION...] <configfile>
-d, --debug ：debug模式，测试配置文件是否有错误。
-f, --force ：强制转储文件。
-m, --mail=command ：压缩日志后，发送日志到指定邮箱。
-s, --state=statefile ：使用指定的状态文件。
-v, --verbose ：显示转储过程。
```

主流Linux发行版上都默认安装有logrotate包，如果你的linux系统中找不到logrotate, 可以使用apt-get或yum命令来安装

```bash
yum install -y logrotate
```

## logrotate日志切割

### logrotate配置过程

在/etc/logrotate.d下创建tomcat-direct文件，内容如下

```bash
/data/direct-tomcat-8501/logs/catalina.out{ #要切割的日志路径，如果是多个就用空格分隔
su root root
daily #切割文件的周期，比如每天，每周，每月。**当天并不执行！**
rotate 30 #最多保留日志文件的个数
compress #切割的文件是否进线压缩
missingok #忽略错误
dateext #使用当前日期作为命名格式
dateyesterday #用昨天的日期命名dateext
notifempty #当日志为空的时候，不进行切割
copytruncate #用于已经打开的日志文件，先复制再清空，可能会丢失部分日志
size 20M #满足文件大小即执行，注意测试时候设置太小会不执行
}

dateformat .%Y%m%d-%H点    #配合dateext使用，紧跟在下一行出现，定义文件切割后的文件名，必须配合dateext使用，只支持 %Y %m %d %s
olddir /var/log/tomcat/oldlog  #轮替后日志文件放入指定的目录 
```

### 参数说明

以下信息可通过man logrotate获取

| 配置参数                 | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| **daily**                | 日志文件将按天轮循                                           |
| **weekly**               | 日志文件将按周轮循                                           |
| **monthly**              | 日志文件将按月轮循。其它可用值还有yearly。                   |
| **dateext**              | 归档旧版本的日志文件，添加像 YYYYMMDD 这样的每日扩展名       |
| **rotate 5**             | 一次将存储5个归档日志。对于第六个归档，时间最久的归档将被删除。 |
| **compress**             | 在轮循任务完成后，已轮循的归档将使用gzip进行压缩。           |
| **delaycompress**        | 总是与compress选项一起用，delaycompress选项指示logrotate不要将最近的归档压缩，压缩将在下一次轮循周期进行。这在你或任何软件仍然需要读取最新归档时很有用。 |
| **missingok**            | 在日志轮循期间，任何错误将被忽略，例如“文件无法找到”之类的错误。 |
| **notifempty**           | 如果日志文件为空，轮循不会进行。                             |
| **create 644 root root** | 以指定的权限创建全新的日志文件，同时logrotate也会重命名原始日志文件。 |
| **prerotate/endscript**  | 在切分之前需要执行的命令可以放入这个对，这两个关键字必须单独成行 |
| **postrotate/endscript** | 在所有其它指令完成后，postrotate和endscript里面指定的命令将被执行。在这种情况下，rsyslogd 进程将立即再次读取其配置并继续运行。 |
| **size**                 | 满足size就滚动一次，（没有滚动周期概览，n次）                |
| **maxsize**              | 每满足maxsize就滚动一次，不满足则滚动1次，（每个滚动周期内，n次或1次） |
| **minsize**              | 满足minsize则滚动一次，不满足则滚动0次，(每个滚动周期内，1次或0次) |
| **maxage**               | 自动删除掉超过maxage指定天数的归档文件                       |

### 测试logrotate

```bash
logrotate /etc/logrotate.conf #调用/etc/lograte.d/下配置的所有日志
logrotate -vfd /etc/logrotate.d/tomcat-direct #调用指定的配置
# -v显示详情 -f 强制分割日志 -d 调试配置,结果输出,不进行真实分割操作

cat /var/lib/logrotate/logrotate.status #可以查看到各配置文件具体的执行时间
gzip -d filename #查看解压缩的日志包
```

### logrotate自动执行的时间配置

由于依赖于cron的执行，可以通过修改 /etc/anacrontab的参数来配置。注意！此配置会影响所有通过logrotate轮训的日志

```bash
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1       5       cron.daily              nice run-parts /etc/cron.daily #每天3点5分开始或者3点5+45开始，delay+RANDOM_DELAY即5+45。nice命令将该进程设置为nice=10，默认为0，即低优先级进程。如果RANDOM_DELAY=0，那么表示准确延迟5min，即03:05执行cron.daily内的脚本。
7       25      cron.weekly             nice run-parts /etc/cron.weekly
@monthly 45     cron.monthly            nice run-parts /etc/cron.monthly
```

### 逻辑流程

crond服务加载/etc/cron.d/0hourly --->在每小时的01分执行/etc/crond.hourly/0anacron --->执行anacron --->根据/etc/anacrontab的配置执行/etc/cron.daily，/etc/cron.weekly，/etc/cron.monthly --->执行/etc/cron.daily/下的logrotate脚本 --->执行logrotate --->根据/etc/logrotate.conf配置执行脚本/etc/logrotate.d/tomcat-direct --->分割日志成功
原文链接：https://blog.csdn.net/qq_26711103/article/details/107049757
https://www.haozhuji.com.cn/jc/jingyan/64332.html
https://blog.csdn.net/sinat_36358653/article/details/107390349
