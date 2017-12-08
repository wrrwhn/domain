+++
date = "2017-07-23T22:07:46+08:00"
title = "Go.GPM"
draft = false
tags = ["整理","Go"]
share = true
+++


[TOC]

## 参考
- [gpm - Go Package Manager](https://github.com/pote/gpm)
- [跟我一起写Makefile](http://wiki.ubuntu.org.cn/%E8%B7%9F%E6%88%91%E4%B8%80%E8%B5%B7%E5%86%99Makefile)
- [Creating a personal access token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

## 介绍
### GPM
- 通过名为 `Godeps` 的 manifest 文件来管理、获取引用的指定版本依赖
- `Godeps`文件置于 go 应用程序的根目录

## 使用
### 安装
- wget https://raw.githubusercontent.com/pote/gpm/v1.4.0/bin/gpm --no-check-certificate && chmod +x gpm && sudo mv gpm /usr/local/bin

### 设置
- 更新到指定版本
	- `github.com/nu7hatch/gotrail v0.0.2`
- 注释
	- `#`右侧的均被注释掉
- 扩展
	- `#[gpm-track] xxxx`
		- 总是更新为最新版本
	- GPM 核心忽略行，但会影响插件行为
- 私有资源库访问
	- 创建 github 访问 token
	- 添加下行至 `~/.netrc`
		- `machine github.com login <token>`

### 指令
- gpm [install]
	- 解析`Godeps`文件，获取依赖并设置至合适版本，然后安装
- gpm get
	- 解析`Godeps`文件，获取依赖并设置至合适版本，但`不安装`
- gpm version
- gpm help

### 插件
- gpm-bootstrap 
	- Creates an initial Godeps file	
	- official
- gpm-git 
	- Git management helpers	
	- third party
- gpm-link 
	- Dependency vendoring	
	- third party
- gpm-local 
	- Usage of local paths for packages	
	- third party
- gpm-prebuild 
	- Improves building performance	
	- third party
- gpm-all 
	- Installs multiple sets of deps	
	- official
- gpm-lock 
	- Lock down dependency versions
	- third party