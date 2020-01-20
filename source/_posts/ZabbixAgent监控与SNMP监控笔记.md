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
cd c:\Program Files\zabbix_agent-4.0.16-windows\bin
zabbix_agentd.exe --install -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.win.conf"
#或者 这两句一样
zabbix_agentd.exe -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.win.conf" -i
#卸载服务
zabbix_agentd.exe -d -c "c:\Program Files\zabbix_agent-4.0.16-windows\conf\zabbix_agentd.win.conf"
```
**启动服务**，可以使用图形界面启动service，也可以使用命令行启动
```bash
net start "Zabbix Agent" #启动服务
net stop "Zabbix Agent" #停止服务
```
### Centos
参考《Zabbix实战》

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