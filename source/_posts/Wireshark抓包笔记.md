---
title: Wireshark抓包笔记
date: 2021-08-18 22:37:23
tags: [网络]
---

## Wireshark的安装
Wireshark的官方网站https://www.wireshark.org/
Wireshark支持Windows、Linux、Unix系统，是个开源免费软件。
以下笔记内容为Wireshare的Windows版本V3.4.7

---

## 使用方法
打开Wireshark后，会出现捕获界面，显示的是所有的网络接口情况，包含有线、蓝牙、虚拟网卡。直接双击需要监视的项，就可以进入捕获网络接口信息的状态。
<!--more-->	
#### 常用的过滤使用方法
过滤IP地址
ip.addr == 192.168.1.1
过滤源IP地址、目的地址
ip.src == 1.1.1.1
ip.dst == 1.1.1.1
端口过滤
tcp.port == 80
tcp.srcport == 80
tcp.dstport == 80
协议过滤
直接输入协议名称，如SIP

---

## Wireshark抓包分析之sip
根据主叫号码查询到sip消息（注意新版本的需要使用引号）
sip.from.user == 13800000001
根据被叫号码查询到sip消息（注意新版本的需要使用引号）
sip.to.user == 13800000001
根据包含内容（如软电话号码）查找sip消息（注意新版本的需要使用引号）
sip contains 70001

过滤之后，点击任一消息，得到Call-ID，再根据Call-ID获取这个通话的所有sip消息过滤结果
sip.Call-ID == "856d5c37-b891-1239-2d9a-f0d4e2eb7bdc"

通过坐席侧的数据只能看到与ASBC公网地址的交互信息，通过ASBC上的数据流量可以看到到ASBC到平台sip代理的交互信息，甚至可以知道此通话从哪台语音网关呼入呼出的。首先同样根据以上的过滤方式，在ACK请求消息中

Session Initation Protocol(ACK)
Message Body
Session Description Protocol
Owner/Creator,Session Id(o)后面可以找到网关和媒体服务的IP地址

---

## 使用Wireshark抓包QQ语音定位IP地址
使用Wireshark抓取一段QQ语音通话的流量，注意必须接通才可以。
使用Ctrl+F或者点击工具栏里的放大镜按钮，选择第三个下拉框选择“字符串”，第一个下拉框选择“分组详情”，第二个下拉框选择“宽窄”，在后面搜索框中输入“020048”。就能在记录里找到对方的目的IP地址，再根据目的IP地址来查找对应所在位置。