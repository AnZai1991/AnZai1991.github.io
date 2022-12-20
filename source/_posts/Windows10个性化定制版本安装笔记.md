---
title: Windows10个性化定制版本安装笔记
date: 2019-11-22 0:39:45
tags: [操作系统,Windows]
---

## 一、光盘镜像文件：
1、使用NTLite软件制作定制的ISO安装文件，我一般精简的内容如下：

**组件移除：**
Metro应用
OneDrive（微软云盘）
MicrosoftEdge
MicrosoftEdgeDevToolsClient
SmartScreen
Windows Defender
XboxGameCallableUI
零售演示内容
屏保
Embedded Mode（嵌入模式）
Windows Recovery（Windows恢复）
安全中心
<!--more-->
**功能设置：**
Windows Search（没起作用）

**设置：**
OneDrive  禁用
Windows Defender禁用
控制面板  启用
用户文件夹 启用
计算机   启用
允许应用使用联系人 禁用
允许应用使用账户名称，图片及其他账户信息  禁用
向微软发送有关……写入功能 禁用
启用体验改善计划（NVIDIA驱动） 禁用
预安装应用 禁用

2、使用软件UltraISO将制作好的ISO文件写入U盘中
![图片](/assets/img/article_2/QQ截图20191119102732.png)
![图片](/assets/img/article_2/QQ截图20191119102802.png)

## 二、安装步骤：
首先，用windows安装盘启动电脑，到下面这个界面，按Shift+F10，就会启动具有管理员权限的ＣＭＤ
![图片](/assets/img/article_2/QQ截图20191119102818.png)
进入CMD程序后，依次输入以下命令：
1.diskpart（启动Diskpart程序）
2.list disk （查看电脑中有哪些磁盘）
3.select disk 0（选中编号为0的磁盘）
4.clean（清除磁盘所有分区）
5.convert gpt（将磁盘转换成GPT格式）
6.list partition（查看当前磁盘分区情况）
7.create partition efi size=100（默认大小为M）
8.create partition msr size =128
9.create partition primary size =102400(此处为你想设置C盘的大小)
10.两次输入exit

## 三、优化步骤（个人喜好仅作参考）：
1.1安装驱动（右键windows图标→设置→更新和安全→Windows更新）
  官网下载驱动
1.2安装必备组件（.net3.5、C++运行库、DirectX Microsoft图形库）
  .net3.5可以通过程序里的打开关闭windows功能安装
或者dism.exe /online /enable-feature /featurename:NetFX3 /Source:(安装盘盘符):\sources\sxs

2.1安装Office
2.2激活Windows和Office
2.3更改计算机名（设置→系统→关于→重命名这台电脑）
2.4更改锁屏方式（设置→账户→登录选项）

3.1控制面板-系统和安全-更改用户账户控制设置-从不通知
  控制面板-系统和安全-Windows Defender防火墙-打开
  关闭方法：选择关闭→左边有个高级设置→中间有个Windows防火墙属性→关闭。服务里也可以禁用Windows Firewall
3.2服务→禁用Windows Search（因为everything更好用）/Superfetch
  服务→打开Security Center/Windows update/Windows Firewall
ps:Windows自带的关于安全的软件有操作中心、Security Center、Windows Firewall、Windows Defender

4.1文件夹选项将所有隐藏全部打开
  关闭休眠：powercfg -h off
4.2修改虚拟内存到非系统盘

5.1关闭打开文件提示：
  运行→gpedit.msc→用户配置→管理模板→Windows组件→附件管理器→双击中等危险文件类型包含列表
  选择已启用→指定中等危险扩展名里输入.exe
5.2纯个人喜好，把视频音乐图片下载分别指向别的盘符。
ps：运行msconfig可以进行引导项的更改

6.1创建Windows10 自动还原点
右键此电脑，属性，系统保护，选中C盘，配置，启用系统保护，设置磁盘空间使用量。创建
6.2Windows设置-个性化-任务栏-选择哪些图标显示在任务栏-通知区域始终显示所有图标

Windows 8.1
1、Chrome
2、火绒、CCleaner（捆绑了avast）、Uninstall Tool
3、Rainmeter
Windows10
1、Chrome
2、火绒、CCleaner、Uninstall Tool
3、Rainmeter
4、游戏相关（暴雪战网、WeGame、炉石传说、炉石传说盒子）
5、腾讯QQ、向日葵、百度云盘、Bandzip、pandownload、office、potplayer
6、NTLite