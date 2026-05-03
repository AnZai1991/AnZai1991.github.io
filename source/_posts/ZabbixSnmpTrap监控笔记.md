---
title: ZabbixSnmpTrap监控笔记
date: 2026-05-03 11:13:42
tags: [Linux,运维]
---

## 环境介绍

操作系统：Centos 7.9
软件环境：zabbix-server 4.2.8、NET-SNMP version 5.7.2、snmpttv1.4beta2
网关型号：鼎信通达MTG2000-X-8E1



## 一、核心逻辑

**网关(MTG2000-X-8E1)发送 Trap -> Zabbix Proxy(安装snmptrapd和snmptt) 上的 snmptrapd 接收 -> snmptt 翻译并过滤 -> 写入日志文件 -> Zabbix Server 读取日志并触发告警**

<!--more-->

## 二、网关配置

- **启用 SNMP 协议**：设置为 Yes。
- **SNMP 版本**：选择 v2c。
- **SNMP 管理主机 IP**（Trap 接收器地址）：填写 **Zabbix Proxy 的 IP**。
- **Trap 端口**：默认使用标准 162（手册中可能与 NTP 端口描述混淆，实际按 SNMP 标准配置 162）。
- **Community 字符串**：设置 Read Community（轮询用，建议自定义如 `public` 或更安全的字符串）；Write Community 如果需要。
- **注意**：MTG2000-X-8E1的trap消息可能只能通过管理口GE0发送

原厂提供的snmp-trap信息

| OID                       |                       |                                                              |                                               |                                                              |
| ------------------------- | --------------------- | ------------------------------------------------------------ | --------------------------------------------- | ------------------------------------------------------------ |
| 1.3.6.1.4.1.38111.7.30.1  | SystemUpTrap          | An indication that the system is already up and running.     | 系统已经启动，正在运行                        | System Up                                                    |
| 1.3.6.1.4.1.38111.7.30.2  | E1StatusTrap          | This Notification is generated when there is a change of mtg E1 port status | E1端口状态发生变化通知                        | port %u miss signal port %u have Remote Alarm port %u have AIS Alarm port %u have ISDN/SS7/R2 Signal Alarm port %u is normal SS7/PRA/R2 port %u  do not configure |
| 1.3.6.1.4.1.38111.7.30.3  | PRIlinkdownTrap       | This Notification is generated when there is a change of PRI link status | pri链路状态发生变化通知                       | PRI port %u link down PRI port %u link up                    |
| 1.3.6.1.4.1.38111.7.30.4  | SS7linkdownTrap       | This Notification is generated when there is a change of SS7 link status | ss7链路状态发生变化通知                       | SS7 port %u link down SS7 port %u link up                    |
| 1.3.6.1.4.1.38111.7.30.5  | SIPTrunkStatusTrap    | This Notification is generated when there is a change of SIP Trunk status | sip中继状态发生变化通知                       | SIPTrunkNO:%u,TrunkName:%s,PeerIP:%s,Status:Fault SIPTrunkNO:%u,TrunkName:%s,PeerIP:%s,Status:Ok |
| 1.3.6.1.4.1.38111.7.30.6  | SystemRestartTrap     | An indication that the system is in the process being restarted | 系统重启通知                                  | System Restart                                               |
| 1.3.6.1.4.1.38111.7.30.7  | WanIpUpdatedTrap      | This Notification is generated when there is a change of the IP address of WAN interface | 设备wan口ip地址更改通知                       | Wan Ip Updated                                               |
| 1.3.6.1.4.1.38111.7.30.12 | TelnetLogin3TFailTrap | This Notification is generated when Telnet Login failed for three times | telnet登录连续失败3次通知                     | Telnet login failed within three times                       |
| 1.3.6.1.4.1.38111.7.30.13 | HttpLogin3TFailTrap   | This Notification is generated when Http Login failed for three times | web登录连续失败3次通知                        | Http login failed within three times                         |
| 1.3.6.1.4.1.38111.7.30.17 | R2linkdownTrap        | This Notification is generated when there is a change of R2 link status | R2中继cas复帧同步状态变化通知                 | R2 port %u link down R2 port %u link up                      |
| 1.3.6.1.4.1.38111.7.30.18 | PowerStatustrap       | notification of mtg power status                             | mtg 电源状态告警                              | 电源 %u 正常，电压增大 Power %u is good, voltage increases. 电源 %u 正常，电压减小 Power %u is good, voltage decreases. 电源 %u 正常 Power %u is good. 电源 %u 故障，无电压 Power %u is fault, no voltage. 电源 %u 故障，链路故障 Power %u is fault, link down. 电源 %u 故障 Power %u is fault. |
| 1.3.6.1.4.1.38111.7.30.19 | RoleSwitchtrap        | notification of mtg role switch to main board                | mtg mcu板切换为主板                           | MCU%u 切换为主用板 MCU%u switch to main board.               |
| 1.3.6.1.4.1.38111.7.30.21 | SipacctRegisterTrap   | This Notification is generated when there is a change of SIP Accoutn status | sip账号状态发生变化通知                       | Sip Trunk:%d, AcctId:%d, Register Status:%s                  |
| 1.3.6.1.4.1.38111.7.30.22 | CloudRegisterTrap     | This Notification is generated when there is a change of Cloud status | cloud网管注册状态发生变化通知                 | Cloud Server Register Status:%s                              |
| 1.3.6.1.4.1.38111.7.30.23 | CommonEventTrap       | Common Event info trap                                       | 通用事件通知，支持配置是否发送。包括：dtu故障 | Common Event:login success from web! Common Event:login success from telnet! Common Event:login success from other type! |
| 1.3.6.1.4.1.38111.7.30.24 | Q931UsageOverThrtrap  | This Notification is generated when pri call over trreshold  | Q931使用率超阈值告警                          | Q931 Trunk(No.%u)usage rate restore normal Q931 Trunk(No.%u)usage rate over threshold Q931 Trunk(No.%u)usage rate continuous over threshold |
| 1.3.6.1.4.1.38111.7.30.25 | CpuTemperatureTrap    | This Notification is generated when cpu temperature over threshold | 设备cpu温度超阈值告警                         | dev cpu temperature is %uC, temperature exception! dev cpu temperature is %uC, temperature normal! |
| 1.3.6.1.4.1.38111.7.30.26 | FanStatustrap         | notification of mtg fan status                               | mtg 风扇状态告警                              | [alarm_events] The fan is abnormal and the speed is 0. [alarm_events] Fan returns to normal. |



## 三、zabbix proxy

### 安装必要软件包

```bash
yum install -y net-snmp net-snmp-utils snmptt perl-Sys-Syslog perl-Text-ParseWords net-snmp-perl
```



### 配置zabbix代理（zabbix_proxy.conf）

/etc/zabbix/zabbix_proxy.conf

```conf
# 开启 SNMP Trapper 功能
StartSNMPTrapper=1

# 指定 snmptt 写入的日志路径
SNMPTrapperFile=/var/log/snmptt/snmptt.log

# 如果不使用snmptt翻译过滤，则指定 snmptrap 写入的日志路径
#SNMPTrapperFile=/var/log/snmptrap/snmptrap.log
```



### 配置snmptrapd（接收端snmptrapd.conf）

/etc/snmp/snmptrapd.conf

```conf
# Example configuration file for snmptrapd
#
# No traps are handled by default, you must edit this file!
#
# authCommunity   log,execute,net public
# traphandle SNMPv2-MIB::coldStart    /usr/bin/bin/my_great_script cold

# 定义团体名
authCommunity log,execute,net public

# 将所有接收到的 Trap 转发给 snmptt 处理
traphandle default /usr/sbin/snmptthandler
```

#### 导致snmptt.log一直不产生的根源！！

```bash
ps aux | grep snmptrapd #可以看到有没有加-On参数,没有的话需要修改配置增加
vim /usr/lib/systemd/system/snmptrapd.service
systemctl restart snmptrapd
```

增加-On参数，确认 snmptrapd 启动参数里有 -On（以数字形式输出 OID），这个很关键
ExecStart=/usr/sbin/snmptrapd $OPTIONS -f -On
/usr/lib/systemd/system/snmptrapd.service

```conf
[Unit]
Description=Simple Network Management Protocol (SNMP) Trap Daemon.
After=syslog.target network.target

[Service]
Type=notify
Environment=OPTIONS="-Lsd"
EnvironmentFile=-/etc/sysconfig/snmptrapd
ExecStart=/usr/sbin/snmptrapd $OPTIONS -f -On
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target
```



### 配置snmptt（转换器snmptt.ini和snmptt.conf）

#### 修改全局配置/etc/snmp/snmptt.ini

```conf
[General]
snmptt_system_name = $A #此处不设置或注释掉snmptt会自动使用trap发送方的IP地址
mode = daemon # Daemon（守护进程）模式
multiple_event = 1
# 启用 DNS 解析（如果需要显示主机名而非 IP）
dns_enable = 1
strip_domain = 0

strip_domain_list = <<END
domain.com
END

resolve_value_ip_addresses = 0

# 设置为 1 以启用来自 UCD-SNMP / NET-SNMP 包的 Perl 模块。  
# 这是 $v 变量替换能够正常工作的必要条件，同时也是此 .ini 文件中启用的某些其他选项的要求。  
# 设置为 0 以禁用来自 UCD-SNMP / NET-SNMP 包的 Perl 模块。  
# 注意：在独立模式下启用此设置可能会导致 SNMPTT 运行非常缓慢，因为在启动时需要加载 MIBS。
net_snmp_perl_enable = 0
net_snmp_perl_cache_enable = 1
net_snmp_perl_best_guess = 0
translate_log_trap_oid = 0 # OID 被翻译成了文字形式会导致匹配失败！
translate_value_oids = 0 # OID 被翻译成了文字形式会导致匹配失败！
translate_enterprise_oid_format = 1
translate_trap_oid_format = 1
translate_varname_oid_format = 1
translate_integers = 1
#mibs_environment = ALL
wildcard_expansion_separator = " "
allow_unsafe_regex = 0
remove_backslash_from_quotes = 0
dynamic_nodes = 0
description_mode = 0
description_clean = 1
threads_enable = 0
threads_max = 10
date_time_format = %H:%M:%S %Y/%m/%d

[DaemonMode]
daemon_fork = 1
daemon_uid = snmptt
pid_file = /var/run/snmptt.pid
spool_directory = /var/spool/snmptt/
sleep = 5
use_trap_time = 1
keep_unlogged_traps = 1
duplicate_trap_window = 0

[Logging]
stdout_enable = 0
log_enable = 1
# 设置日志格式（Zabbix 要求的格式）
log_file = /var/log/snmptt/snmptt.log
log_system_enable = 0
log_system_file = /var/log/snmptt/snmpttsystem.log
# 确保开启了未知 Trap 记录（用于调试）
unknown_trap_log_enable = 1
# 看看未知 Trap 记录到了哪个文件
unknown_trap_log_file = /var/log/snmptt/snmpttunknown.log
statistics_interval = 0
syslog_enable = 1
syslog_facility = local0
syslog_level_debug = <<END
END
syslog_level_info = <<END
END
syslog_level_notice = <<END
END
syslog_level_warning = <<END
END
syslog_level_err = <<END
END
syslog_level_crit = <<END
END
syslog_level_alert = <<END
END
syslog_level = warning
syslog_system_enable = 1
syslog_system_facility = local0
syslog_system_level = warning

[SQL]
db_translate_enterprise = 0
db_unknown_trap_format = '$-*'
sql_custom_columns = <<END
END

sql_custom_columns_unknown = <<END
END

mysql_dbi_enable = 0
mysql_dbi_host = localhost
mysql_dbi_port = 3306
mysql_dbi_database = snmptt
mysql_dbi_table = snmptt
mysql_dbi_table_unknown = snmptt_unknown
mysql_dbi_table_statistics = 
mysql_dbi_username = snmpttuser
mysql_dbi_password = password
mysql_ping_on_insert = 1
mysql_ping_interval = 300
postgresql_dbi_enable = 0
postgresql_dbi_module = 0
postgresql_dbi_hostport_enable = 0
postgresql_dbi_host = localhost
postgresql_dbi_port = 5432
postgresql_dbi_database = snmptt
postgresql_dbi_table_unknown = snmptt_unknown
postgresql_dbi_table_statistics = 
postgresql_dbi_table = snmptt
postgresql_dbi_username = snmpttuser
postgresql_dbi_password = password
postgresql_ping_on_insert = 1
postgresql_ping_interval = 300
dbd_odbc_enable = 0
dbd_odbc_dsn = snmptt
dbd_odbc_table = snmptt
dbd_odbc_table_unknown = snmptt_unknown
dbd_odbc_table_statistics = 
dbd_odbc_username = snmptt
dbd_odbc_password = password
dbd_odbc_ping_on_insert = 1
dbd_odbc_ping_interval = 300

[Exec]
exec_enable = 1
pre_exec_enable = 1
unknown_trap_exec = 
unknown_trap_exec_format = 
exec_escape = 1

[Debugging]
DEBUGGING = 2 # 改为更详细的日志级别
DEBUGGING_FILE = 
 DEBUGGING_FILE = /var/log/snmptt/snmptt.debug
DEBUGGING_FILE_HANDLER = 

[TrapFiles]
snmptt_conf_files = <<END
/etc/snmp/snmptt.conf
/etc/snmp/snmptt_MTG2000.conf
END
```



#### 默认规则/etc/snmp/snmptt.conf

格式：

```conf
EVENT 事件名字 OID "类别" 严重性
FORMAT 格式化输出字符串   (定义写入本地日志文件的内容或监控系统中显示的最终描述文字。可以用 $1, $2 等变量引用 Trap 带回的具体参数。)
EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["SystemUpTrap"] -o "System Up"
（可选）执行外部命令或脚本。需要使用zabbix trapper模式才执行，snmp trapper是根据匹配日志的FORMAT来输出的
SDESC/EDESC   标准描述（Standard Description）的开始和结束，仅作为对该事件的解释说明。
```

/etc/snmp/snmptt.conf

```conf
#
#
#设备冷启动
EVENT coldStart .1.3.6.1.6.3.1.1.5.1 "Status Events" Normal
FORMAT Device reinitialized (coldStart)
#EXEC qpage -f TRAP notifygroup1 "Device reinitialized (coldStart)"
SDESC
A coldStart trap signifies that the SNMPv2 entity, acting
in an agent role, is reinitializing itself and that its
configuration may have been altered.
EDESC
#
#
#设备热启动
EVENT warmStart .1.3.6.1.6.3.1.1.5.2 "Status Events" Normal
FORMAT Device reinitialized (warmStart)
#EXEC qpage -f TRAP notifygroup1 "Device reinitialized (warmStart)"
SDESC
A warmStart trap signifies that the SNMPv2 entity, acting
in an agent role, is reinitializing itself such that its
configuration is unaltered.
EDESC
#
#
#链路断开
EVENT linkDown .1.3.6.1.6.3.1.1.5.3 "Status Events" Normal
FORMAT Link down on interface $1.  Admin state: $2.  Operational state: $3 
#EXEC qpage -f TRAP notifygroup1 "Link down on interface $1.  Admin state: $2.  Operational state: $3"
SDESC
A linkDown trap signifies that the SNMP entity, acting in
an agent role, has detected that the ifOperStatus object for
one of its communication links is about to enter the down
state from some other state (but not from the notPresent
state).  This other state is indicated by the included value
of ifOperStatus.
EDESC
#
#
#链路恢复
EVENT linkUp .1.3.6.1.6.3.1.1.5.4 "Status Events" Normal
FORMAT Link up on interface $1.  Admin state: $2.  Operational state: $3
#EXEC qpage -f TRAP notifygroup1 "Link up on interface $1.  Admin state: $2.  Operational state: $3"
SDESC
A linkUp trap signifies that the SNMP entity, acting in an
agent role, has detected that the ifOperStatus object for
one of its communication links left the down state and
transitioned into some other state (but not into the
notPresent state).  This other state is indicated by the
included value of ifOperStatus.
EDESC
#
#
#认证失败
EVENT authenticationFailure .1.3.6.1.6.3.1.1.5.5 "Status Events" Normal
FORMAT SNMP athentication failure
#EXEC qpage -f TRAP notifygroup1 "SNMP authentication failure"
SDESC
An authenticationFailure trap signifies that the SNMPv2
entity, acting in an agent role, has received a protocol
message that is not properly authenticated.  While all
implementations of the SNMPv2 must be capable of generating
this trap, the snmpEnableAuthenTraps object indicates
whether this trap will be generated.
EDESC

```



#### 自定义规则/etc/snmp/snmptt_MTG2000.conf

/etc/snmp/snmptt_MTG2000.conf

```conf
# MTG2000-X-8E1 SNMP Trap Definitions

EVENT SystemUpTrap .1.3.6.1.4.1.38111.7.30.1 "Status Events" Normal
FORMAT ZBXTRAP $aA 系统已经启动，正在运行 - the system is already up and running
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["SystemUpTrap"] -o "System Up"
SDESC
An indication that the system is already up and running.
EDESC

EVENT E1StatusTrap .1.3.6.1.4.1.38111.7.30.2 "Status Events" Normal
FORMAT ZBXTRAP $aA E1端口状态发生变化通知: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["E1StatusTrap"] -o "port $* miss signal"
SDESC
E1 port status change notification
EDESC

EVENT PRIlinkdownTrap .1.3.6.1.4.1.38111.7.30.3 "Status Events" Normal
FORMAT ZBXTRAP $aA pri链路状态发生变化通知: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["PRIlinkdownTrap"] -o "PRI port $* link down"
SDESC
PRI link status change notification
EDESC

EVENT SS7linkdownTrap .1.3.6.1.4.1.38111.7.30.4 "Status Events" Normal
FORMAT ZBXTRAP $aA SS7链路状态发生变化通知: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["SS7linkdownTrap"] -o "SS7 port $* link down"
SDESC
SS7 link status change notification
EDESC

EVENT SIPTrunkStatusTrap .1.3.6.1.4.1.38111.7.30.5 "Status Events" Normal
FORMAT ZBXTRAP $aA sip中继状态发生变化通知: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["SIPTrunkStatusTrap"] -o "SIPTrunkNO:$1,TrunkName:$2,PeerIP:$2,Status:Fault"
SDESC
SIP Trunk status change notification
EDESC

EVENT SystemRestartTrap .1.3.6.1.4.1.38111.7.30.6 "Status Events" Major
FORMAT ZBXTRAP $aA 系统重启通知: System Restart
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["SystemRestartTrap"] -o "System Restart"
SDESC
System restart notification
EDESC

EVENT WanIpUpdatedTrap .1.3.6.1.4.1.38111.7.30.7 "Status Events" Normal
FORMAT ZBXTRAP $aA 设备wan口ip地址更改通知: Wan Ip Updated
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["WanIpUpdatedTrap"] -o "Wan Ip Updated"
SDESC
WAN IP address change notification
EDESC

EVENT TelnetLogin3TFailTrap .1.3.6.1.4.1.38111.7.30.12 "Security Events" Major
FORMAT ZBXTRAP $aA Telnet登录连续失败3次
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["TelnetLogin3TFailTrap"] -o "Telnet login failed 3 times"
SDESC
Telnet login failed for three times
EDESC

EVENT HttpLogin3TFailTrap .1.3.6.1.4.1.38111.7.30.13 "Security Events" Major
FORMAT ZBXTRAP $aA Web登录连续失败3次
#EXEC /usr/lib/zabbix/alertscript/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["HttpLogin3TFailTrap"] -o "HttpLogin failed 3 times"
SDESC
Http login failed for three times
EDESC

EVENT PowerStatustrap .1.3.6.1.4.1.38111.7.30.18 "Status Events" Normal
FORMAT ZBXTRAP $aA mtg 电源状态告警: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["PowerStatustrap"] -o "$*"
SDESC
notification of mtg power status
EDESC

EVENT CaoWeiBanTrap .1.3.6.1.4.1.38111.7.30.20 "Status Events" Major
FORMAT ZBXTRAP $aA 槽位板类型 DTU状态: $*
SDESC
This Notification is generated when system restart
EDESC

EVENT CpuTemperatureTrap .1.3.6.1.4.1.38111.7.30.25 "Status Events" Major
FORMAT ZBXTRAP $aA 设备cpu温度超阈值告警: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["CpuTemperatureTrap"] -o "$*"
SDESC
This Notification is generated when cpu temperature over threshold
EDESC

EVENT FanStatustrap .1.3.6.1.4.1.38111.7.30.26 "Status Events" Major
FORMAT ZBXTRAP $aA mtg 风扇状态告警: $*
#EXEC /usr/lib/zabbix/alertscripts/zabbix_sender -z 10.239.3.50 -s "10.206.5.2" -k snmptrap["FanStatustrap"] -o "$*"
SDESC
notification of mtg fan status
EDESC

EVENT GE0-1Trap .1.3.6.1.4.1.38111.7.30.28 "Status Events" Major
FORMAT ZBXTRAP $aA GE0管理口状态和GE1业务口状态: $*
SDESC
This Notification is Status of GE0 and GE1
EDESC
```



**配置说明：**

- **：代表打印所有接收到的变量。**
- **1,1,**2…**：代表打印 Trap 包中第 N 个变量。例如 SIP 中继告警，使用具体的变量位置可以让 Zabbix 的正则匹配更高效。**
- **严重性 (Severity)**：这里的 Normal/Warning/Critical 仅作为 snmptt 日志标识，最终告警等级由 Zabbix Trigger 决定。



改完配置重启所有服务

```bash
systemctl enable snmptrapd snmptt zabbix-proxy
systemctl restart snmptrapd snmptt zabbix-proxy
```



```bash
\# 创建目录和文件
mkdir -p /var/log/snmptrap
touch /var/log/snmptrap/snmptrap.log

\# 设置权限（zabbix用户需要读取权限）
chown zabbix:zabbix /var/log/snmptrap/snmptrap.log
chmod 644 /var/log/snmptrap/snmptrap.log
```



## 四、zabbix server配置

#### 配置-主机-创建主机

#### 配置-模板-创建模板

编辑模板，编辑模板宏

{ $IFCONTROL }⇒1

{ $IF_ERRORS_WARN }⇒2

{ $IF_UTIL_MAX }⇒95

{ $SNMP_COMMUNITY }⇒public

#### 创建监控项

- **SystemUpTrap** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.1"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.1"].count(300,already up and running) }=1
- **E1端口状态** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.2"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap["1.3.6.1.4.1.38111.7.30.2"].regexp("信号丢失|信令告警|告警") }=1
  - { Template MTG2000 SNMP Traps v2c:snmptrap["1.3.6.1.4.1.38111.7.30.2"].regexp("E1端口|正常") }=1
  - 触发器问题事件生成模式：多重
- **PRI链路状态** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.3"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap["1.3.6.1.4.1.38111.7.30.3"].regexp("PRI端口|故障") }=1
  - { Template MTG2000 SNMP Traps v2c:snmptrap["1.3.6.1.4.1.38111.7.30.3"].regexp("PRI端口|正常") }=1
  - 触发器问题事件生成模式：多重
- **SS7链路状态** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.4"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.4"].regexp("SS7端口|故障") }=1
  - { Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.4"].regexp("SS7端口|正常") }=1
  - 触发器问题事件生成模式：多重
- **SIP中继状态** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.5"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.5"].regexp("Status:Fault") }=1
  - { Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.5"].regexp("Status:Ok") }=1
  - 触发器问题事件生成模式：多重
- **系统重启** 
  - 类型：SNMP trap
  - 键值：`snmptrap["1.3.6.1.4.1.38111.7.30.6"]`
  - 信息类型：log
  - 日志时间格式：hh:mm:ss yyyy/MM/dd
  - 触发器：{ Template MTG2000 SNMP Traps v2c:snmptrap[".1.3.6.1.4.1.38111.7.30.6"].regexp("System Restart") }=1


其中E1\PRI\SS7\SIP或其他监控项因为接收到的是中文日志，需要在zabbix server做预处理的，在监控项-进程中增加JavaScript，参数如下

```javascript
// 输入的 value 是整行日志，例如 "...通知: 45 31 E7 AB AF..."
return value.replace(/([0-9A-F]{2}\s+){2,}[0-9A-F]{2}/gi, function(match) {
    try {
        // 1. 将空格分隔的十六进制字符串转为字节数组
        var hexArr = match.trim().split(/\s+/);
        var bytes = new Uint8Array(hexArr.length);
        for (var i = 0; i < hexArr.length; i++) {
            bytes[i] = parseInt(hexArr[i], 16);
        }
        
        // 2. 使用 UTF-8 解码字节流
        // Zabbix 的 JavaScript 环境支持标准的 TextDecoder
        return new TextDecoder('utf-8').decode(bytes);
    } catch (e) {
        // 如果转换失败（例如不是合法的UTF8），返回原内容
        return match;
    }
});
```



#### 创建触发器

1. **正则表达式提取**： 鼎信通达的 Trap 信息包含 `%u`（端口号）。
2. **手动关闭告警**： 部分 Trap 只有告警没有明确的恢复报文（例如 `SystemRestartTrap`），这类触发器应配置为 `Allow manual close`（允许手动关闭）。



#### 选择主机关联模板



## 五、验证

```ini
# /etc/snmpt/snmptt.ini
# 确保开启了未知 Trap 记录（用于调试）
unknown_trap_log_enable = 1

# 看看未知 Trap 记录到了哪个文件
unknown_trap_log_file = /var/log/snmptt/snmpttunknown.log

# 检查日志模式（确保为 1）
log_enable = 1
log_file = /var/log/snmptt/snmptt.log
```



```bash
# 在 Proxy 本机执行，模拟网关 10.206.5.2 发送一个系统启动告警
snmptrap -v 2c -c public 127.0.0.1 "" .1.3.6.1.4.1.38111.7.30.1

snmptrap -v 2c -c public 127.0.0.1 '' .1.3.6.1.4.1.38111.7.30.1 .1.3.6.1.2.1.1.3.0 s "System Up"

snmptrap -v 2c -c public 127.0.0.1 "" .1.3.6.1.4.1.38111.7.30.1 .1.3.6.1.6.3.1.1.4.1.0 o .1.3.6.1.4.1.38111.7.30.1

#模拟真实网关地址发送
snmptrap -v 2c -c public 127.0.0.1 "" .1.3.6.1.4.1.38111.7.30.2 \
  .1.3.6.1.4.1.38111.7.30.2.1 s "port 1 miss signal" \
  .1.3.6.1.6.3.18.1.3.0 a 10.206.5.2
#这里 .1.3.6.1.6.3.18.1.3.0（snmpTrapAddress）会告诉 snmptrapd 真实来源是 10.206.5.2。

snmptrap -v 2c -c public 127.0.0.1 "" .1.3.6.1.4.1.38111.7.30.2 \
  .1.3.6.1.4.1.38111.7.30.2.1 s "port 1 is normal" \
  .1.3.6.1.6.3.18.1.3.0 a 10.206.5.2
  
snmptrap -v 2c -c public 127.0.0.1 "" .1.3.6.1.4.1.38111.7.30.6 \
  .1.3.6.1.4.1.38111.7.30.6.1 s "System Restart" \
  .1.3.6.1.6.3.18.1.3.0 a 10.206.5.2

```

实时调试，在zabbix proxy上实时查看系统日志过滤

```bash
tail -f /var/log/messages | grep -E "snmptrapd|snmptt"
ls /var/spool/snmptt/ #检查目录下是否有积压的文件

#编辑snmptt.ini启用调试
mkdir -p /var/log/snmptt
chmod 755 /var/log/snmptt
chown snmptt:snmptt /var/log/snmptt
chmod 664 /var/log/snmptt/snmptt.log

tail -f /var/log/snmptt/snmptt.debug
tail -f /var/log/snmptt/snmptt.log
tail -f /var/log/snmptt/snmpttunknown.log

# 检查 Zabbix 代理日志
tail -f /var/log/zabbix/zabbix_proxy.log
# 检查 Zabbix Server 日志
tail -f /var/log/zabbix/zabbix_server.log | grep -i trap

```

如果有产生/var/log/snmptt/snmpttunknown.log，而没有产生/var/log/snmptt/snmptt.log。说明OID 过滤规则（EVENT 路径）没匹配上
