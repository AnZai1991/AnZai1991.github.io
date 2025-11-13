---
title: ProxmoxVirtualEnvironment之外网访问frp内网穿透
date: 2025-11-13 12:49:42
tags: [Linux，运维]
---

## 环境介绍
操作系统：ProxmoxVE_8.4
Windows服务端：frp_0.63.0
Linux客户端：frp_0.63.0

---

## 端口解析
域名→公网IP:443→nginx代理到127.0.0.1:自定义端口8XXX
域名:自定义端口→公网IP:自定义端口→frp代理到192.168.XXX.XXX:自定义端口8XX6

## 服务端（云服务器）
<!--more-->
windows安装包frp_0.63.0_windows_amd64.zip
```bash
#frps.toml服务端配置文件内容
bindPort = 7000
vhostHTTPSPort = 8XX6
```

阿里云ECS安全组需要添加入方向策略，新增自定义TCP7000和8XX6端口

```bash
#CMD命令行启动命令
D:\frp_0.63.0_windows_amd64\frps.exe -c D:\frp_0.63.0_windows_amd64\frps.toml
```

## 客户端（PVE）

linux系统安装包frp_0.63.0_linux_amd64.tar.gz

```bash
#frpc.toml客户端配置文件内容
serverAddr = "公网IP地址替代"
serverPort = 7000
auth.token = "2z***4s***w***7fz***"

[[proxies]]
name = "web"
type = "https"
localIP = "192.168.XXX.XXX"
localPort = 8XX6
customDomains = ["www.XXX.cn"]
```

客户端在/root/frp_0.63.0_linux_amd64下，启动命令如下
```bash
cd /root/frp_0.63.0_linux_amd64
./frpc -c ./frpc.toml
```

### 创建systemd自启动文件
```bash
#ayb-frp.service配置文件内容
[Unit]
Description=FRPC
After=network.target

[Service]
WorkingDirectory=/root/frp_0.63.0_linux_amd64
ExecStart=/opt/frp.sh

[Install]
WantedBy=multi-user.target
```

```bash
#frp.sh脚本内容
#!/bin/bash
/root/frp_0.63.0_linux_amd64/frpc -c /root/frp_0.63.0_linux_amd64/frpc.toml
```

### 设置开机自启动

```bash
systemctl enable ayb-frp.service
```

参考文档：
https://github.com/fatedier/frp/blob/dev/README_zh.md
https://gofrp.org/zh-cn/docs/overview/