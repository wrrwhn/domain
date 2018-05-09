+++
date = "2018-05-04T12:00:00+08:00"
title = "Python.Pack"
draft = false
tags = ["整理","Python"]
share = true
+++

[TOC]

# 类型
- .py
	- 项目**源码**
	- 需要安装 Python 及依赖库
- .pyc
	- Python 解释器可识别的**二进制码**
	- 跨平台
	- 需要安装 Python 及依赖库
- .exe
	- 不同平台的**可执行文件**


# 工具比较
|     Solution    | Windows | Linux | OS X | Python 3 | One file mode | Zipfile import | Eggs | pkg_resources support |
|-----------------|---------|-------|------|----------|---------------|----------------|------|-----------------------|
| bbFreeze        | yes     | yes   | yes  | no       | no            | yes            | yes  | yes                   |
| py2exe          | yes     | no    | no   | yes      | yes           | yes            | no   | no                    |
| **pyInstaller** | yes     | yes   | yes  | no       | yes           | no             | yes  | no                    |
| **cx_Freeze**   | yes     | yes   | yes  | yes      | no            | yes            | yes  | no                    |
| py2app          | no      | no    | yes  | yes      | no            | yes            | yes  | yes                   |


# 详解
## pyInstaller

### 注意事项
- **依赖于打包环境**，无法于 x64 环境打 x86 的包
>
Pyinstaller produces a binary depending from the python you used to build it. 
So if you use python 2.7 64 bit it is not possible, as far as I know, to produce a 32 bit executable.

### 指令
- 安装
	- `pip[3] install pyinstaller`
- 版本
	- `pyinstaller --version`
- 执行
	- `pyinstaller [option] *.py`
	- 参数
		- `-F`
			- 打包出**可独立运行 exe**
		- `-c`
			- 使用**控制台**，无界面
			- **默认项**
		- `-w|--noconsole`
			- 去除控制台窗口，仅带 GUI 界面时推荐
			- cmd 中执行时**不显示相关日志**
		- `-i`
			- 指定可执行文件的**图标**
	- 示例
		- `pyinstaller -F -i favicon.ico pack.py`
			- ![pack-exe.png](http://otzm88f21.bkt.clouddn.com/3d05119a-51e4-4dd9-b015-dd230f4d11fd.png)
- 查看文件列表
	- `pyi-archive_viewer *.exe`
	- 示例
		- `pyi-archive_viewer pack.exe`
			```
			pos, length, uncompressed, iscompressed, type, name
			[(0, 255, 323, 1, 'm', 'struct'),
			 (255, 1104, 1814, 1, 'm', 'pyimod01_os_path'),
			 (1359, 4287, 9268, 1, 'm', 'pyimod02_archive'),
			 (5646, 7221, 18489, 1, 'm', 'pyimod03_importers'),
			 (12867, 1859, 4171, 1, 's', 'pyiboot01_bootstrap'),
			 (14726, 86, 103, 1, 's', 'pack'),
			 (14812, 47536, 87888, 1, 'b', 'VCRUNTIME140.dll'),
			 (62348, 39531, 87552, 1, 'b', '_bz2.pyd'),
			 (101879, 623839, 1443840, 1, 'b', '_hashlib.pyd'),
			 (725718, 85308, 247296, 1, 'b', '_lzma.pyd'),
			 (811026, 28848, 65536, 1, 'b', '_socket.pyd'),
			 (839874, 782920, 1737216, 1, 'b', '_ssl.pyd'),
			 (1622794, 480, 1029, 1, 'b', 'pack.exe.manifest'),
			 (1623274, 83821, 180736, 1, 'b', 'pyexpat.pyd'),
			 (1707095, 1522436, 3555840, 1, 'b', 'python36.dll'),
			 (3229531, 9124, 19968, 1, 'b', 'select.pyd'),
			 (3238655, 356822, 899072, 1, 'b', 'unicodedata.pyd'),
			 (3595477, 0, 0, 0, 'o', 'pyi-windows-manifest-filename pack.exe.manifest'),
			 (3595477, 196507, 734166, 1, 'x', 'base_library.zip'),
			 (3791984, 1150741, 1150741, 0, 'z', 'out00-PYZ.pyz')]
			 ```
- 查看可执行文件依赖动态库
	- `pyi-bindepend *.exe`
	- 示例
		- `pyi-bindepend pack.exe`
			```
			pack.exe {'KERNEL32.dll', 'WS2_32.dll'}
			```

## 其它
- 暂略


# 参考
## 官网
- [PyInstaller](https://www.pyinstaller.org/)
- [PyInstaller.Manual](https://pyinstaller.readthedocs.io/en/v3.3.1/)
- [PyInstaller.Using](http://pyinstaller.readthedocs.io/en/stable/usage.html)

## 教程
- [pyinstaller简洁教程](http://legendtkl.com/2015/11/06/pyinstaller/)
- [Python程序打包成exe可执行文件](http://blog.csdn.net/zengxiantao1994/article/details/76578421)

## 交叉编译
- [http://www.alivepea.me/prog/pyinstaller/](http://www.alivepea.me/prog/pyinstaller/)
- [基于wine的linux交叉编译python程序](http://haishenming.xyz/%E4%BB%A3%E7%A0%81%E7%9B%B8%E5%85%B3/2018/01/26/wine-python-to-exe/)

## 补充
- [Can I control the architecture (32bit vs 64bit) when building a pyinstaller executable?](https://stackoverflow.com/questions/7155866/can-i-control-the-architecture-32bit-vs-64bit-when-building-a-pyinstaller-exec)
- [使用PyInstaller打包Python程序](http://ju.outofmemory.cn/entry/137370)
- []()
- []()
- []()
- []()