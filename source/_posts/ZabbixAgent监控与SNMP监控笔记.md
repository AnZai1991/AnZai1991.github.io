---
title: ZabbixAgent监控与SNMP监控笔记
date: 2020-01-20 21:38:25
tags: Linux
---

## ZabbixAgent
官方下载地址：https://www.zabbix.com/cn/download_agents#tab:40LTS
### Windows OS
将下载文件保存到本地，解压缩到C:\Program Files\路径下
**配置zabbix_agentd.conf**，参考《Zabbix实战》
```bash
Server=127.0.0.1,203.152.200.115 #被动地址，Zabbix-server的IP地址
ServerActive=127.0.0.1,203.152.200.115 #主动模式，Zabbix-server的IP地址
Hostname=Zabbix server #本机的Host那么，使用主动模式必须配置
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
### Windows 2008 R2
![图片](/assets/img/article_6/1.png "SNMP Services")
2008 R2需要重启，service中双击SNMP Service，在安全选项中，添加团体名public，选择接受来自任何主机的SNMP数据包，或者可以指定主机。
![图片](/assets/img/article_6/2.png "SNMP Services")
最后关闭防火墙或者配置下防火墙即可。
### Windows 2003
![图片](/assets/img/article_6/3.png "SNMP Services")
其他与Win2008一样
### SUSE Linux 11
参考《Zabbix实战》