---
title: 计算机网络之SIP协议
date: 2021-11-25 14:43:06
tags: [网络]
---

## 协议简介
SIP（Session Initiation Protocol，会话初始协议）是由IETF（Internet Engineering Task Force，因特网工程任务组）制定的多媒体通信协议。广泛应用于CS（Circuit Switched，电路交换）、NGN（Next Generation Network，下一代网络）以及IMS（IP Multimedia Subsystem，IP多媒体子系统）的网络中，可以支持并应用于语音、视频、数据等多媒体业务，同时也可以应用于Presence（呈现）、Instant Message（即时消息）等特色业务。可以说，有IP网络的地方就有SIP协议的存在。
SIP是类似于HTTP，SIP可以减少应用特别是高级应用的开发时间。由于基于IP协议的SIP利用了IP网络，固定网运营商也会逐渐认识到SIP技术对于他们的远意义。
<!--more-->
---

## 两种会话模式
### Session Model
会话中，对于消息体内容大于1300字节时，一般采用Session Model。
主叫方A呼叫被叫方B：
步骤1：主叫方A发送INVITE请求到代理服务器；
步骤2：代理服务器发送100 Trying 响应主叫方A；
步骤3-6：代理服务器搜索被叫方B的地址，获取地址后转发INVITE请求；
步骤7-9：被叫方B生成的180 振铃响应，返回给主叫方A；
步骤10-12：被叫方B生成的200 OK响应，返回给主叫方A；
步骤13-17：主叫方A收到被叫方B200 OK响应后，向被叫方B发送一个ACK，会话建立；
步骤18-20：会话结束后，任何参与者（A或B）都可以发送一个BYE请求来终止会话；
步骤21-23：主叫方A发送200 OK响应来确认BYE，会话终止。
注：以上的整个流程称之为一个Dialog
### Pager Model
在Sip消息中，对于消息体不大于1300字节时，一般采用Pager Model。Sip消息通信中采用MESSAGE方法，MESSAGE方法本身并不建立Dialog，在多数应用中，每条IM消息都是独立的，颇似分页消息。
其余内容可参考https://www.cnblogs.com/xiaxveliang/p/12434170.html

---

## SIP消息体格式
SIP消息体结构与Http协议结构相似，均由三部分组成
### 请求行(request-line) or 状态行(status-line)
请求行格式：Method Request-URI SIP-Version CRLF（CRLF消息头结束的空行标志）
请求行举例：REGISTER sip:ACD.ZTE.COM.CN SIP/2.0
SIP规定了六种method：INVITE ACK CANCEL OPTIONS BYE REGISTER

状态行状态行格式： SIP-Version Status-Code Reason-Phrase CRLF（CRLF消息头结束的空行标志）
状态行举例：SIP/2.0 200 OK /r/n
Status-Code状态码：
状态代码由3位数字组成，表示请求是否被理解或被满足。

### 消息头(message header)
|  Header   | 含义说明  | 举例  |
|  ----  | ----  | ----  |
| Call-ID  | 由本地设备(Client)生成，全局唯一，每次呼叫这个值唯一不变 | Call-ID: asd88asd77a@1.2.3.4 |
| From  | 表示请求的发起者 | From: sip:user1@domain.com;tag=49583 |
| To  | 表示请求的接收者 | To: sip:user2@domain.com |
| Via  | Via头域是被服务器插入request中，用来检查路由环的，并且可以使response根据via找到返回的路 | Via: SIP/2.0/TCP user1pc.domain.com;branch=z9hG4bK776sgdkse |
| Max-Forwards  | 用于表示这个包最多可以传送多少跳，每经过一跳都会减一当Max-Forwards==0系统会返回483。默认为70 | Max-Forwards: 70 |
| Contact  | 包含源的URI信息，用来给响应方直接和源建立连接用 | Contact: sip:192.168.100.1:1111 |
| Content-Type  | 指明消息体的类型 (SDP会话描述协议) | Content-Type: text/plain；Content-Type: application/sdp; Content-Type: application/cpim; |
| Content-Length  | 指明消息体的字节大小 | Content-Length: 18 |

### 正文(message body)
其余内容可参考https://www.cnblogs.com/xiaxveliang/p/12434170.html
或https://blog.csdn.net/u014257614/article/details/23456107