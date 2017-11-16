+++
date = "2017-08-09T22:07:46+08:00"
title = "Git.Install"
draft = false
tags = ["整理","Git"]
share = true
+++

[TOC]

# Git 安装
## 参考
- [GotGitHub](http://www.worldhello.net/gotgithub)
- [git - 简易指南](http://www.bootcss.com/p/git-guide/)
- [GitHub Help](https://help.github.com/)
- [Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

## 安装
### Windows
- `git config --global user.name wrrwhn`
- `git config --global user.email yqjdcyy@gmail.com`
- `git config --global alias.gcf git config`
- `ssh-keygen`
    - 创建公钥/私钥对（id_rsa.pub/id_rsa） - 注意，不要有空格！！！
- `ssh-keygen -C "yqjdcyy@gmail.com" -f ~/.ssh/yqjdcyy`
    - 创建gotgithub公私钥对

### Linux
- `yum install git git-svn git-email git-gui gitk`
- `git --version`
- `git config --global user.name "wrrwhn"`
- `git config --global user.email yaoqingju@gmail.com`
- `cat ~/.gitconfig`

## 注
-  git 2.0 版本安装后生成 ssh-key 的方式建议使用 ssh-keygen -t rsa -C "humingx@yeah.net"，然后三个回车搞定。
