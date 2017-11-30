+++
date = "2017-11-23T22:07:46+08:00"
title = "Git.Subtree"
draft = false
tags = ["整理","Git"]
share = true
+++

## 参考
- [Git Subtree的使用](http://www.jianshu.com/p/3096069e9b72)
- [Git Tools - Subtree Merging](https://git-scm.com/book/en/v1/Git-Tools-Subtree-Merging)
- [用 Git Subtree 在多个 Git 项目间双向同步子项目，附简明使用手册](https://tech.youzan.com/git-subtree/)


## 描述
- 简介
> 经由 Git Subtree 来维护的子项目代码，对于父项目来说是透明的，**所有的开发人员看到的就是一个普通的目录**，原来怎么做现在依旧那么做，只需要维护这个 Subtree 的人在合适的时候去做同步代码的操作。

- 适用情况
	- 使用 github 的 page 功能实现项目主页功能，结合 hugo 生成 public 文件夹后同步到 gh-pages 分支，作为分支完整内容
	- 项目 A 和项目 B 存在公用模块，A 中的修改、新增等操作，均同步至项目 B


- 可用方案
	- `Git Submodule`
		- Git 官方早期推荐方案
		- 允许其它仓库指定 commit 嵌入仓库子目录
			- 但需要 `init` 和 `update`
		- 产生 `.gitmodule` 文件记录 `submodule` 版本信息
		- 删除较费劲
	- `Git Subtree`
		- Git 1.5.2 版本后推荐
	- `npm`
		- node package manager
	- `composer`
		- npm.php 版本


## 指令
- usage
	- `git subtree add   --prefix=<prefix> <commit>`
	- `git subtree add   --prefix=<prefix> <repository> <ref>`
	- `git subtree merge --prefix=<prefix> <commit>`
	- `git subtree pull  --prefix=<prefix> <repository> <ref>`
	- `git subtree push  --prefix=<prefix> <repository> <ref>`
	- `git subtree split --prefix=<prefix> <commit...>`

- commons.option
	`-h, --help`
		- 显示帮助文档
	`-q`
		- 静默执行
	`-d`
		- 显示 Debug 信息
	`-P, --prefix ...`
		- 将要分离出去的子目录名称
	`-m, --message ...`
		- 使用给定信息作为提交合并提交的备注信息


- [add|merge|pull].option
	- `--squash`
		- 将子树提交合并为一次提交

- split.option
	- `--annotate ...`
		- 为新的提交消息添加前缀
	- `-b, --branch ...`
		- 为子目录新建一分支
	- `--ignore-joins`
		- 优先忽略 `--rejoin` 提交
	- `--onto ...`
		- 尝试将新目录与已存在的目录进行关联
	- `--rejoin`
		- 合并提交至新分支的起点


## 实例
- 前情提要
	- 项目 P1、P2 拥有共同的项目 S

- 初始化项目 S
	- `cd P1`
	- `git subtree add --prefix=[path for S] [git path for S] [branch]`

- 更新项目 S
	- `git subtree push --prefix=[path for S] [git path for S] [branch]`
		- 遍历针对 S 目录的更改，一并提交到项目 S 的 git 上

- 拉取项目 S
	- `git subtree pull --prefix=[path for S] [git path for S] [branch]`

- 创建新起点
	- `git subtree split --rejoin --prefix[path for S] --branch [new git path for S]`
	- `git push [git path for S] [new git path for S]:master`