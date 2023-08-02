---
title: Linux系统之Crontab
date: 2023-08-01 13:10:03
tags: [Linux,运维]
---

## 环境介绍

操作系统：CentOS Linux release 7.6.1810 (Core)
软件版本：

## crontab简介

crontab命令常见于Unix和类Unix的操作系统之中，用于设置周期性被执行的指令。该命令从标准输入设备读取指令，并将其存放于“crontab”文件中，以供之后读取和执行。
<!--more-->
## crontab操作命令

crontab -l 显示当前用户的任务计划
crontab -e 编辑当前用户的任务计划
crontab -r 删除当前用户的任务计划
tail -f /var/log/cron 查看crontab日志
