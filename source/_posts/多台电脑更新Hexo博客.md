---
title: 多台电脑更新Hexo博客
date: 2019-12-17 12:01:26
tags: [办公]
---
在网上找了很多的解决方案，采用了最常见的利用git分支的方法来实现。参考网址：https://www.jianshu.com/p/0b1fccce74e0

---

## 原理
创建新的分支XXX来存储hexo的环境文件，而推送到远端的静态文件（在public目录下）会依然根据配置文件中的配置，推送到master分支下。这样当自己需要在新电脑上更新Hexo博客的时候，只需要搭建好必须的环境，然后通过git clone将新分支XXX的环境文件clone到本地，就可以在本地完成Hexo的更新。
<!--more-->

---

## 部署多机环境操作步骤
1、在GitHub的仓库下新建新的分支XXX，切换到该分支，并在该仓库->Settings->Branches->Default branch中将默认分支设为XXX，将该仓库克隆到本地。
2、进入username.github.io文件目录，使用Git Bash执行git branch命令查看当前所在分支是否是XXX。将本地博客的部署文件**（Hexo目录下的全部文件）**全部拷贝进username.github.io文件目录中去。
3、进入username.github.io文件目录下，将该目录下的全部文件提交到XXX分支，提交之前注意:
将themes目录中的主题的.git目录删除（如果有），因为一个git仓库中不能包含另一个git仓库，提交主题文件夹会失败。
tips：删除了themes目录中的.git就不能git pull更新主题了，需要更新主题时在另一个地方git clone下来该主题的最新版本，然后将内容拷到当前主题目录即可。
4、执行
```bash
git add .
git commit -m 'backup hexo files'（引号内容可改）
git push
```
即可将博客的hexo部署环境提交到GitHub个人仓库的XXX分支。

master分支和XXX分支各自保存着一个版本，master分支用于保存博客静态资源，提供博客页面供人访问；XXX分支用于备份博客部署文件，供自己维护更新，两者在一个GitHub仓库内互不冲突。

5、在新机器上添加生成的ssh key到GitHub账户下，在新电脑上克隆username.github.io仓库的XXX分支到本地，此时本地git仓库处于XXX分支。切换到username.github.io目录，执行npm install(由于仓库有一个.gitignore文件，里面默认是忽略掉node_modules文件夹的，也就是说仓库的XXX分支并没有存储该目录，所以需要install下)

---

## 多机更新操作
部署的过程完成，以下是在新设备上的操作流程。
首先在新设备上依次安装搭建好node.js、Hexo框架、git。
**注意：我的GitHub上只能使用旧版本node.js12.13.1，文章后面有检查更新的方法**
然后将自己的GitHub账号配置给git

```bash
git config --global user.name "github注册的用户名"   #设置 Git 用户名
git config --global user.email "github注册的邮箱"   #设置 Git 邮箱

git config --global user.name   #确认您正确设置了 Git 用户名
git config --global user.email   #确认您正确设置了 Git 邮箱

gitclone https://github.com/xxxxxx.github.io.git   #克隆https下载
```
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
### hexo检查更新的方法
```bash
npm outdated
#输出中标记红色的为需要更新的
npm update xxxx --latest   #将需要更新的package名替换xxxx
```
参考：http://xiejava.ishareread.com/posts/442ea121/