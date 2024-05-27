---
title: Linux系统之环境变量配置的6种方法
date: 2023-09-20 11:13:30
tags: [运维,Linux]
---
## 环境介绍
操作系统：Ubuntu 20.04.2 LTS
用户名：az
以Java环境变量配置为例

## 概述
因为发现一个很特别的现象，在az用户下java环境正常，切root下就不行，执行source /etc/profile后正常，切az下不正常，在az下执行source /etc/profile后正常，切root又不正常了。所以来研究下环境变量的加载到底是什么情况
Linux读取环境变量
* export命令显示当前系统定义的所有环境变量
* echo $PATH命令输出当前的PATH环境变量的值

其中PATH变量定义了运行命令的查找路径，以冒号:分割不同的路径，使用export定义的时候可加双引号也可不加。
<!--more-->
## export PATH
使用export命令直接修改PATH的值，配置Java环境变量的方法
```bash
export JAVA_HOME=/opt/jvm/jdk1.7.0_80
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
注意事项：
* 生效时间：立即生效
* 生效期限：当前终端有效，窗口关闭后无效
* 生效范围：仅对当前用户有效
* 配置的环境变量中不要忘了加上原来的配置，即$PATH部分，避免覆盖原来配置

## vim ~/.bashrc
通过修改用户目录下的~/.bashrc文件进行配置

注意事项：
* 生效时间：使用相同的用户打开新的终端时生效，或者手动source `~/.bashrc`生效
* 生效期限：永久有效
* 生效范围：仅对当前用户有效
* 如果有后续的环境变量加载文件覆盖了PATH定义，则可能不生效

## vim ~/.bash_profile
和修改~/.bashrc文件类似，也是要在文件最后加上新的路径即可
注意事项：
* 生效时间：使用相同的用户打开新的终端时生效，或者手动source `~/.bash_profile`生效
* 生效期限：永久有效
* 生效范围：仅对当前用户有效
* 如果没有`~/.bash_profile`文件，则可以编辑`~/.profile`文件或者新建一个

## vim /etc/bashrc
该方法是修改系统配置，需要管理员权限（如root）或者对该文件的写入权限
```bash
chmod -v u+w /etc/bashrc
vim /etc/bashrc
#在最后一行添加
```
注意事项：
* 生效时间：新开终端生效，或者手动source /etc/bashrc生效
* 生效期限：永久有效
* 生效范围：对所有用户有效

## vim /etc/profile
该方法修改系统配置，需要管理员权限或者对该文件的写入权限，和vim /etc/bashrc类似
```bash
chmod -v u+w /etc/profile
vim /etc/profile
#在最后一行添加
```
注意事项：
* 生效时间：新开终端生效，或者手动source /etc/profile生效
* 生效期限：永久有效
* 生效范围：对所有用户有效

## vim /etc/environment
该方法是修改系统环境配置文件，需要管理员权限或者对该文件的写入权限
```bash
chmod -v u+w /etc/environment
vim /etc/profile
#在最后一行添加
```
注意事项：
* 生效时间：新开终端生效，或者手动source /etc/environment生效
* 生效期限：永久有效
* 生效范围：对所有用户有效

## Linux环境变量加载原理解析
### 环境变量分类
环境变量可以简单的分成用户自定义的环境变量以及系统级别的环境变量。
* 用户级别环境变量定义文件：`~/.bashrc`、`~/.profile`（部分系统为：`~/.bash_profile`）
* 系统级别环境变量定义文件：/etc/bashrc、/etc/profile(部分系统为：/etc/bash_profile）、/etc/environment

另外在用户环境变量中，系统会首先读取`~/.bash_profile`（或者`~/.profile`）文件，如果没有该文件则读取`~/.bash_login`，根据这些文件中内容再去读取`~/.bashrc`。

### 测试Linux环境变量加载顺序的方法
为了测试各个不同文件的环境变量加载顺序，我们在每个环境变量定义文件中的第一行都定义相同的环境变量UU_ORDER，该变量的值为本身的值连接上当前文件名称。
需要修改的文件如下：
* /etc/environment
* /etc/profile
* /etc/profile.d/test.sh 新建文件，没有文件夹可略过
* /etc/bashrc或者/etc/bash.bashrc
* `~/.bash_profile`或者`~/.profile`
* `~/.bashrc`
在每个文件中的第一行都加上下面这句代码，并相应的把冒号后的内容修改为当前文件的绝对文件名。
……此后省略……

### Linxu环境变量文件加载详解
由上面的测试可容易得出Linux加载环境变量的顺序如下：
系统环境变量 -> 用户自定义环境变量 /etc/environment -> /etc/profile -> `~/.profile`
打开/etc/profile文件你会发现，该文件的代码中会加载/etc/bash.bashrc文件，然后检查/etc/profile.d/目录下的.sh文件并加载。
其次再打开`~/.profile`文件，会发现该文件中加载了`~/.bashrc`文件。
从`~/.profile`文件中代码不难发现，/.profile文件只在用户登录的时候读取一次，而/.bashrc会在每次运行Shell脚本的时候读取一次。

### 一些小技巧
可以自定义一个环境变量文件，比如在某个项目下定义uusama.profile，在这个文件中使用export定义一系列变量，然后在`~/.profile`文件后面加上：sourc uusama.profile，这样你每次登陆都可以在Shell脚本中使用自己定义的一系列变量。
也可以使用alias命令定义一些命令的别名，比如alias rm="rm -i"（双引号必须），并把这个代码加入到`~/.profile`中，这样你每次使用rm命令的时候，都相当于使用rm -i命令，非常方便。

参考：https://www.xjx100.cn/news/633902.html?action=onClick