---
title: "Python.Conda"
date: "2018-03-26"
categories:
 - "整理"
tags:
 - "Python"
 - "Conda"
toc: true
---


# 作用
- 用于科学计算的Python发行版
- 提供了**包管理与环境管理**的功能
- 方便地解决多版本python并存、切换以及各种第三方包安装问题

# 分类
- conda
	- 工具
	- 提供包管理和环境管理
	- 将所有工具、第三方包、python 和 conda自身**均当成 package 对待**
- Anaconda
	- **打包集合**
		- 预装 conda
		- 特地版本 python
		- 众多 apckages
		- 科学计算工具
- Miniconda
	- 基础版本
		- **仅包含 python 与 conda**

# 安装
- 参考
	- [Installation—Conda documentation](https://conda.io/docs/user-guide/install/index.html)
	- [Installing on Windows](https://conda.io/docs/user-guide/install/windows.html#install-win-silent)
- 确认
	- ![目录.png](http://otzm88f21.bkt.clouddn.com/ad77cd1f-3eed-4f86-af30-72c94f17eeeb.png)
	- ![查看安装列表.png](http://otzm88f21.bkt.clouddn.com/2e746d90-be7e-4d77-8aa5-badb9fb0c1ab.png)

# 指令
## 环境管理
- 新增
	- `conda create -n <env_name> <package_names|python[=version]>`
- 删除
	- `conda env remove -n <env_name>`
- 列表
	- `conda env list`
- 进入
	- **Windows**
		- `activate <env_name>`
	- OSX | Linux
		- `source activate <env_name>`
- 退出
	- **Windows**
		- `deactivate`
	- OSX | Linux
		- `source deactivate`
- 导出配置
	- `conda env export > </path/environment.yaml>`
- 引入配置
	- `conda env update -f=/path/environment.yml`

## 包管理
- 安装
	- `conda install [-n <env_name>] <package_name[=<version>]>`
- 卸载
	- `conda remove [-n <env_name>] <package_names>`
- 更新
	- `conda upgrade [-n <env_name>] <package_names>`
	- `conda upgrade [-n <env_name>] --all`
	- `conda upgrade [-n <env_name>] [python|conda|anaconda]`
- 列表
	- `conda list`

## 镜像管理
- 添加
	- 
	- 示例
		- 添加「清华TUNA」镜像源至仓库
		- `conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/`
		- `conda config --set show_channel_urls yes`

## 补充
- PIP 导出导入配置
	- `pip freeze > <environment.txt>`
	- `pip install -r </path/requirements.txt>`


# 参考
- 安装
	- [Installation—Conda documentation](https://conda.io/docs/user-guide/install/index.html)
	- [Installing on Windows](https://conda.io/docs/user-guide/install/windows.html#install-win-silent)
- 使用
	- [Getting started with Anaconda](https://docs.anaconda.com/anaconda/user-guide/getting-started)
	- [Python for Visual Studio Code](https://docs.anaconda.com/anaconda/user-guide/tasks/integration/python-vsc)
	- [Anaconda使用总结](http://python.jobbole.com/86236/)
	- [Conda](https://conda.io/docs/index.html)
	- [Anaconda Distribution](https://docs.anaconda.com/anaconda/)
- 指令
	- [初学python者自学anaconda的正确姿势是什么？？](https://www.zhihu.com/question/58033789)
	- [Conda工具使用](https://www.jianshu.com/p/17288627b994)
- 补充
	- [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/)
