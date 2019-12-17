---
title: 多台电脑更新Hexo博客
date: 2019-12-17 12:01:26
tags:
---
在网上找了很多的解决方案，采用了最常见的利用git分支的方法来实现。参考网址：https://www.jianshu.com/p/0b1fccce74e0

---

## 原理
创建新的分支XXX来存储hexo的环境文件，而推送到远端的静态文件（在public目录下）会依然根据配置文件中的配置，推送到master分支下。这样当自己需要在新电脑上更新Hexo博客的时候，只需要搭建好必须的环境，然后通过git将新分支XXX的环境文件pull下来，就可以在本地完成Hexo的更新。
<!--more-->
---

## 操作步骤

部署的过程参考上面的网址，以下主要记录在新设备上的操作流程。
在新设备上搭建好node.js并安装Hexo框架，同时需要安装git工具。
将自己的GitHub账号配置给git
1、通过Git Bash在自己想要存储Hexo的目录下拉取新建分支XXX的内容
```bash
git pull
```
2、编辑更新博客

3、民工三连
```bash
hexo clean
hexo g
hexo s
```

4、本地的测试没问题后，就将当前目录下的所有文件推送到新建分支，依次执行以下
```bash
hexo clean
git add .
git commit -m 'backup hexo files'
git push
```
保证新建分支的版本是最新的。

5、最后再执行一次民工三连，将本地的静态文件推送到master分支，就完成了远端的更新。
```bash
hexo clean
hexo g
hexo d
```