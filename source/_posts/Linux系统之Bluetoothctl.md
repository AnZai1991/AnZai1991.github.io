---
title: Linux系统之Bluetoothctl
date: 2024-04-23 13:05:27
tags: [Linux,办公]
---

## 环境介绍
操作系统：Ubuntu 20.04.2 LTS
硬件型号：Lenovo L450

## 介绍
Bluetoothctl 是一个交互式且易于使用的工具，用于控制蓝牙设备。它是在基于 Linux 的操作系统上管理蓝牙的主要实用程序。本指南将向您展示如何使用 bluetoothctl 在 Linux PC 上轻松设置蓝牙设备和连接。

## 检查蓝牙状态

```bash
sudo systemctl status bluetooth
```
<!--more-->
## 开启蓝牙服务
```bash
sudo systemctl enable bluetooth
sudo systemctl start bluetooth
```

## 扫描附近设备
要主动搜索可以连接的蓝牙设备，请使用扫描命令
```bash
bluetoothctl scan on
```
当您运行上述命令时，您的电脑将查找并列出系统范围内的所有蓝牙设备
所有蓝牙设备都标记为设备，后跟各自的媒体访问控制 (MAC) 地址，这是网络上设备的唯一标识符。 MAC 地址遵循格式 XX : XX : XX : XX : XX : XX。 Bluetoothctl 还显示设备的名称
注意：如果您找不到所需的蓝牙设备，请确保您的系统蓝牙可被发现。
要使其他设备可以发现您的蓝牙适配器，请使用以下命令：
```bash
bluetoothctl discoverable on
```

## 连接到设备
连接蓝牙设备的最简单方法是使用配对命令将其与您的电脑配对
```bash
bluetoothctl pair FC:69:47:7C:9D:A3
```
对于已与您的电脑配对的设备，您以后只需使用connect命令即可连接到它们，如下所示：
```bash
bluetoothctl connect FC:69:47:7C:9D:A3
```

## 列出配对设备
您可以通过运行以下命令来查看当前与系统配对的设备
```bash
bluetoothctl paired-devices
```
您还可以使用以下命令列出计算机蓝牙范围内的设备
```bash
bluetoothctl devices
```

## 信任配对设备
除了与蓝牙设备配对外，您还可以选择信任某些设备，以便将来轻松连接到它们。
要信任蓝牙设备
```bash
bluetoothctl trust FC:69:47:7C:9D:A3
```
您可以通过发出以下命令来取消信任设备：
```bash
bluetoothctl untrust FC:69:47:7C:9D:A3
```

## 断开蓝牙设备
要取消配对蓝牙设备，请使用删除命令
```bash
bluetoothctl remove FC:69:47:7C:9D:A3
```
您还可以使用 bluetoothctl 将设备与系统断开连接
```bash
bluetoothctl disconnect FC:69:47:7C:9D:A3
```
如果您希望阻止特定设备连接到您的系统，可以使用阻止命令，后跟该设备的 MAC 地址。
```bash
bluetoothctl block FC:69:47:7C:9D:A3
```
要取消阻止设备，只需将上述命令中的单词阻止替换为取消阻止即可。

## 使用交互模式
如前所述，bluetoothctl 是一个交互式实用程序。在本指南中，我们使用长命令以避免混淆，但通常您应该使用交互模式，因为它更快、更容易使用。
要进入交互模式，只需运行不带参数的 bluetoothctl 命令
```bash
bluetoothctl
```
切换到交互模式后，您可以发出命令，而无需预先添加 bluetoothctl，要退出交互模式，提示符中键入exit即可。

参考资料：https://cn.linux-console.net/?p=13305