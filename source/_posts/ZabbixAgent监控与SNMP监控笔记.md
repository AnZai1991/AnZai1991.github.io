---
title: ZabbixAgent监控与SNMP监控笔记
date: 2020-01-20 21:38:25
tags: [操作系统,Linux,Windows,网络,运维]
---

## 笔记介绍
记录除了第一次实战中后续遇到的各种设备的监控操作方法。分为**ZabbixAgent**和**SNMP**两种记录。

---

## ZabbixAgent
官方下载地址：https://www.zabbix.com/cn/download_agents#tab:40LTS
官方网站下选择Windows→Any→amd64→选择版本→OpenSSL→Archive（压缩包）或MSI（安装包）

### Windows OS
将下载文件保存到本地，解压缩到C:\Program Files\路径下
**配置zabbix_agentd.conf**，参考《Zabbix实战》

```bash
Server=127.0.0.1,203.152.200.115 #被动地址，Zabbix-server的IP地址
ServerActive=127.0.0.1,203.152.200.115 #主动模式，Zabbix-server的IP地址
Hostname=Zabbix server #本机的Hostname，使用主动模式必须配置
```
<!--more-->
**注册服务**
```bash
#进入目录
cd c:\Program Files\zabbix_agent-4.0.16-windows\bin
#安装服务
zabbix_agentd.exe --install -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.conf"
#或者
zabbix_agentd.exe -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.conf" -i
#启动服务
zabbix_agentd.exe -s -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.conf"
#停止服务
zabbix_agentd.exe -x -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.conf"
#卸载服务
zabbix_agentd.exe -d -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.conf"
#请仔细对照路径和文件名，即使文件名错了也可能成功，造成很难排查是这里的问题
```
**启动服务**，可以使用图形界面启动service，也可以使用上面的命令启动
```bash
net start "Zabbix Agent" #启动服务
net stop "Zabbix Agent" #停止服务
```
**添加系统盘空间监控**
在zabbix上添加主机后，添加监控项。
名称：C盘占用率/C盘容量/C盘已用容量
键值：vfs.fs.size[c:,pused]/vfs.fs.size[c:,total]/vfs.fs.size[c:,used]
信息类型：浮点数/数字（无正负）/数字（无正负）
单位：%/Bytes/Bytes
更新间隔：300s
**添加系统时间校准监控触发器**
名称：时间校准
严重性：信息
表达式上选择添加：监控项上选择系统UTC时间（需要提前添加监控器，其键值为：system.localtime[utc]）
功能：fuzzytime()函数
最后一个（T）：60
结果：=0（意思是说监控项的时间与zabbix服务器的时间误差超过60秒则发送信息）
### Centos 8
以下摘自《Zabbix实战》
```bash
yum installl -y zabbix zabbix-agent #前提是已经下载好zabbix的官方yum源，可以替换成清华大学的源，参考zabiix-server的安装。否则请前去官网下载
```
配置zabbix_agentd.conf
```bash
egrep -v "(^#|^$)" /etc/zabbix/zabbix_agentd.conf
PidFile=/var/run/zabbix/zabbix_agentd.pid
LogFile=/var/log/zabbix/zabbix_agentd.log
LogFileSize=0
Server=127.0.0.1,203.152.200.115 #被动模式，zabbix-server的IP地址
Server=127.0.0.1,203.152.200.115 #主动模式，zabbix-server的IP地址（建议）
Hostname=Zabbix server #本机的Hostname,使用主动模式则必须配置
Include=/etc/zabbix/zabbix_agentd.d/
UnsafeUserParameters=1 #启用特殊字符，用于自定义监控
```
配置完成后，启动zabbix-Agent并加入自启动
```bash
systemctl enable zabbix-agent
systemctl start zabbix-agent
```

---

## SNMP
### 简介
SNMP协议在OSI模型的第七层（应用层）运行，主要支持一下几种基本操作。
Get操作：NMS使用该操作获取Agent的一个或多个参数值
GetNext操作：NMS使用该操作获取Agent的一个或多个参数的下一个参数值
Set操作：NMS使用该操作设置Agent的一个或多个参数值
Response操作：Agent返回一个或多个参数值。该操作是前面三种操作的响应
Trap操作：Agent主动发出的操作，通知NMS有某些事情发生。
前四种操作，设备使用UDP协议，使用161端口发送报文。执行Trap操作时，设备使用UDP协议，采用162端口发送报文。由于收发采用了不同的端口，所以一个设备可以同时作为Agent和NMS。

使用SNMP协议会遇到两个概念及MIB和OID，除了通用的一些映射关系以外，特定的设备还会由厂家提供私有的MIB和OID的对应关系。可以通过snmpwalk命令根据OID来查找SNMP返回的具体数值，同时可以通过snmpget命令根据MIB来获取所对应的“数字形式"的OID值。

OID查询网站：https://www.alvestrand.no/objectid/1.3.6.1.4.html
### Windows 2008 R2
![图片](/assets/img/article_6/1.png "SNMP Services")
2008 R2需要重启，service中双击SNMP Service，在安全选项中，添加团体名public，选择接受来自任何主机的SNMP数据包，或者可以指定主机。
![图片](/assets/img/article_6/2.png "SNMP Services")
最后关闭防火墙或者配置下防火墙即可。
### Windows 2003
![图片](/assets/img/article_6/3.png "SNMP Services")
其他与Win2008一样
### Windows 7
首先在控制面板中将SNMP功能启动
![图片](/assets/img/article_6/4.png "SNMP Services")
在Windows的服务中配置如下（发送身份认证陷阱即是Trap操作）
![图片](/assets/img/article_6/5.png "SNMP Services")
### SUSE Linux 11
参考《Zabbix实战》
### Router
配置→主机→创建主机
使用snmp v2协议，采用Template Network Generic Device SNMPv2模板
PS：简单介绍一下Zabbix中菜单“配置”里的内容
1、主机群组
将同一类型的设备划为一个分组（比如按路由器、交换机、服务器设备类型分类），名称可以包含中文，是zabbix中用来区分不同设备的分组。
2、模板
本身自带了很多针对不同类型设备的监控模板，可以套用，也可以自建监控模板，名称中不能含有中文字符。
3、主机
用于创建设备的选项，必填的有主机名称（可自定义），群组（就是上面提到的主机群组），选择对应的监控方式，最后添加描述。在第二个选项“模板”中，可以直接套用已有的监控模板来使用。第四个选项“宏”，用来定义连接时候使用到的自定义宏，比如使用snmp监控，使用主机宏{$SNMP_COMMUNITY}对应的值为public来设置团体名称。
### 防火墙
防火墙：Juniper SSG550
登录到防火墙管理web，Configuration→Report Settings→SNMP
创建一个新的团体名称，填写团体名，选中write,trap,Including Traffic Alarms，选择对应的SNMP版本。
HOST IP Address填写zabbix server的地址，Netmask填写255.255.255.255，Source Interface里选择zabbix server所在的内网接口，最后保存设置。
在Network→Interfaces→找到对应的接口，在Service Options中选中SNMP，意思是开放此接口的SNMP管理权限。至此防火墙的设置结束！
此处省略在zabbix server上创建主机的过程，与其他设备无异。模板选择Template Net Juniper SNMPv2
参考https://blog.csdn.net/xoofly/article/details/103570417