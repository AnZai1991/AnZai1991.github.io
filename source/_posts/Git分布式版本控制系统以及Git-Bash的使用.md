---
title: Git分布式版本控制系统以及GitHub的使用
date: 2019-12-17 12:32:15
tags: [运维,办公]
---

## Git\GitHub\Git Bash的关系

- Git
  - 一个分布式版本控制系统，用来在本地和远端仓库中管理代码及历史记录。
  - 负责提交、分支、合并、回滚等版本控制操作的核心工具。
  - 可以在没有任何远程托管服务的情况下独立使用。

- GitHub
  - 基于 Git 的在线托管服务平台，提供远程仓库、协作功能（issues、pull request、code review、CI 等）。
  - 不是必须的，你也可以把代码托管在 GitLab、Bitbucket、自己搭的服务器等，或者只在本地使用 Git。
  - 方便多人协作和代码分享。
  <!--more-->
- Git Bash
  - Windows 下的一种“带 Bash 的命令行环境”，通常随 Git for Windows 一起安装。
  - 让你在 Windows 上使用类 Unix 的命令行和 Git 命令；在 macOS/Linux 上直接用 Terminal 即可运行 Git。
  - 也包含一些常用的工具（ssh、scp 等），便于和远程仓库交互。

常见工作流程（简述）
- 本地使用 Git 做版本控制：git init、git add、git commit。
- 关联远端仓库：git remote add origin <仓库地址>。
- 将改动推送到 GitHub：git push -u origin main（或 master）。
- 从 GitHub 获取他人改动：git pull、git fetch、git merge。
- 通过 GitHub 协作：创建分支、发起 Pull Request、代码评审、合并到主分支。

---

## Git

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

 创建完后，会在当前目录下创建一个.git隐藏文件夹 

---

## GitHub

### 添加SSHKey到GitHub
SSH key：用于 Git 传输层的认证（git@github.com 这条路）
认证方式：用你的本地私钥做公钥认证；GitHub 只保存公钥。
主要用途：git clone/pull/push 走 SSH 协议（remote 形如 git@github.com:org/repo.git）。

查看本地电脑C盘的用户目录是否有.ssh目录，看看里面是否有rd_rsa和id_rsa.pub这两个文件。如果没有参考第一篇博客建立的文章中有介绍怎么生成SSH key。或按照以下步骤创建
通过右键菜单中的gitbash here打开命令行
```
#在用户目录下创建
mkdir ~/.ssh

#配置全局的name和email
git config –global user.name “AnZaiXXXX”
git config –global user.email “XXXX@qq.com“

#生成key，推荐使用ed25519
ssh-keygen -t rsa -C XXXX@qq.com
ssh-keygen -t ed25519 -C "XXXX@qq.com"
```
一路回车会默认生成到 ~/.ssh/id_ed25519；建议设置一个 passphrase。
复制 ~/.ssh/id_rsa.pub内容或cat ~/.ssh/id_ed25519.pub
回到GitHub页面： Settings → SSH and GPG keys → New SSH key → 粘贴进去保存（titel可以用主机名命名以便识别公钥）
```
#测试SSH 能否连上 GitHub
ssh -T git@github.com

#设置remote方式为SSH，将ORG/REPO替换为你的仓库例如我的是git@github.com:AnZai1991/AnZai1991.github.io.git
git remote set-url origin git@github.com:ORG/REPO.git

#验证remote方式是否是ssh
git remote -v
```
**常见问题**：
你有多个 key：需要在 ~/.ssh/config 里为 github.com 指定 IdentityFile。
公司网络/代理阻断 22 端口：可改用 SSH over 443（ssh.github.com:443）。

#### 补充：添加Personal Access Token（classic）到GitHub
Personal Access Token（classic）：用于 HTTPS / API 的访问凭证（https://github.com 这条路）
主要用途：
git clone/pull/push 走 HTTPS 协议（remote 形如 https://github.com/org/repo.git ）时，作为“密码”替代品（GitHub 已禁用账号密码推送）。
调用 GitHub API（REST/GraphQL），比如自动化脚本、CI 工具查询/创建 issue、发 release、管理 webhook 等。
认证方式：一个可复制的字符串（泄露风险更像“密码”）。

回到GitHub页面： Settings → Developer Settings → Personal access tokens → tokens(classic) → Generate new token (classic)

### 上传本地项目到GitHub
```
git init //把这个目录变成Git可以管理的仓库
git add sqlist.h //将工作文件修改提交到本地暂存区
git add . //将所有修改过的工作文件提交到本地暂存区
git commit -m "first commit" //把文件提交到仓库，双引号内容为注释
git remote add origin git@github.com:xxxx/xxxx.git //将本地仓库和远程仓库关联
git push -u origin master //把本地仓库的项目推送到远程仓库（github）上
yes
```

将所有项目文件放入仓库的当前目录下
```
git init
git add .
git commit -m "xxx"
```

[推荐git教程——廖雪峰官网](https://www.liaoxuefeng.com/wiki/896043488029600)

---

## Git Bash

### Git Bash中常用的命令
```
#快速查看所有配置及来源
git config --list --show-origin

#注意有三层配置：系统级、全局（用户）级、本地（仓库）级，优先级是本地 > 全局 > 系统。
#逐层查看（仅显示该层级的配置）
#本地仓库（当前仓库中的 .git/config）
git config --local --list
#全局用户级
git config --global --list
#系统级
git config --system --list
```