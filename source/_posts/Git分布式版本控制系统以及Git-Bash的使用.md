---
title: Git分布式版本控制系统以及Git Bash的使用
date: 2019-12-17 12:32:15
tags: [运维,办公]
---
## Git Bash
### 绑定用户
因为Git是分布式版本控制系统，所以需要填写用户名和邮箱作为一个标识，用户和邮箱为github注册的账号和邮箱。

```
git config --global user.name "xxx"
git config --global user.email "12345@qq.com"
```

有了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然你也可以对某个仓库指定的不同的用户名和邮箱。
```
git init	//把当前目录变成git可以管理的仓库
```
<!--more-->
 创建完后，会在当前目录下创建一个.git隐藏文件夹 

---

### 为GitHub设置SSH key
查看电脑中，C盘用户目录是否有.ssh目录，看看里面是否有rd_rsa和id_rsa.pub这两个文件。如果没有参考第一篇博客建立的文章中有介绍怎么生成SSH key。

---

### 上传本地项目到GitHub
```
git init //把这个目录变成Git可以管理的仓库
git add sqlist.h //将工作文件修改提交到本地暂存区
git add . //将所有修改过的工作文件提交到本地暂存区
git commit -m "first commit" //把文件提交到仓库，双引号内容为注释
git remote add origin xxx@github.com:xxxx/xxxx.git //将本地仓库和远程仓库关联
git push -u origin master //把本地仓库的项目推送到远程仓库（github）上
```

1将所有项目文件放入仓库的当前目录下

```
git init
git add .
git commit -m "xxx"
```

---

### 关联GitHub仓库
到github test仓库复制仓库地址，注意点击SSH，也可以使用https协议，SSH速度快一些。
```
git remote add origin xxx@github.com:xxxx/xxxx.git
git push -u origin master
yes
```
[推荐git教程——廖雪峰官网](https://www.liaoxuefeng.com/wiki/896043488029600)