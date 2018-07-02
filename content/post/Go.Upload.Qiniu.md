---
title: "Go.Upload.Qiniu"
date: "2017-09-07"
categories:
 - "整理"
tags:
 - "Go"
 - "Product"
toc: true
---


## 描述
- 将指定 [文件|文件夹|当前文件夹] 的内容上传至 `七牛云`
- 上传完毕后将链接转换为 `Markdown` 格式文本
- 将结果数据更新至`剪贴板`

## 参考
- [Go SDK](https://developer.qiniu.com/kodo/sdk/1238/go)
- [qiniu/api.v7](https://github.com/qiniu/api.v7)
- [package context](https://godoc.org/golang.org/x/net/context)
- [tjgq/clipboard](https://github.com/tjgq/clipboard)
- [package clipboard](https://godoc.org/github.com/tjgq/clipboard)

## 需求
- 将链接中的图片等本地保存后，再重新上传生成链接
- 命令行可全局快速调用，考虑结合 wox 等工具
- 快速选择多个文件等
- 文件夹仅操作当层


## 测试
- 单文件
```
go run main.go -path=C:\Users\Yao\Desktop\76aaa869ly1fi6n1duxn5j21dw0kak5a.jpg

![76aaa869ly1fi6n1duxn5j21dw0kak5a.jpg][http://otzm88f21.bkt.clouddn.com/04cf05bd-ba52-4dee-8fa9-095555f5c7ec.jpg]
```

- 文件夹
```
go run main.go -path=D:\data\soft\OneDrive\Documents\Write\work\云开

[人员列表.xls][http://otzm88f21.bkt.clouddn.com/a.xls]
[六步搞定实地辅导-20170822.md][http://otzm88f21.bkt.clouddn.com/b.md]
[人员列表.txt][http://otzm88f21.bkt.clouddn.com/c.txt]
```

- Exe 方式调用[**异常**]
```
go run upload.go -path=C:\Users\Yao\Desktop\76aaa869ly1fi6n1duxn5j21dw0kak5a.jpg

[新建 Microsoft PowerPoint 演示文稿.pptx][http://otzm88f21.bkt.clouddn.com/19118280-cacb-43f3-98b6-32600ef459b9.pptx]
uplaod.exe%!(EXTRA string=http://otzm88f21.bkt.clouddn.com/a932af7f-837a-4df6-92dd-e80b29c25ab6.exe)
```
## 补充

- path
	- `cd D:\work\git\yao\go\Utils_Go\src\upload\qiniu`
	- `d:`

- config
	- `set gopath=D:\server\go\lib;D:\work\git\yao\go\Hello_Go;D:\work\git\yk\go\pptconverter-gateway\code;D:\work\git\yao\go\Utils_Go;D:\work\git\401\go\Go_WuKong;D:\work\git\yao\go\Utils_Go\src\upload\qiniu`
	- `set path=C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Program Files\WIDCOMM\Bluetooth Software\;C:\Program Files\WIDCOMM\Bluetooth Software\syswow64;D:\server\java\jdk1.8.0_65\bin;D:\server\java\jdk1.8.0_65\jre\bin;D:\server\maven\apache-maven-3.5.0\bin;C:\Program Files\Git\cmd;C:\Program Files\Microsoft SQL Server\130\Tools\Binn\;D:\Program Files\nodejs\;D:\server\go\1.8\bin;D:\server\python\python36;D:\server\python\python36\Scripts;C:\Program Files (x86)\Microsoft Visual Studio\Shared\14.0\VC\bin\amd64;C:\Program Files\dotnet\;D:\server\MinGW\mingw-w64\x86_64-7.1.0-posix-seh-rt_v5-rev2\mingw64\bin;D:\server\bin;C:\Users\Yao\AppData\Local\Microsoft\WindowsApps;C:\Users\Yao\AppData\Roaming\npm;C:\Program Files (x86)\Microsoft VS Code\bin;C:\Users\Yao\AppData\Local\Programs\Fiddler;D:\server\bin`
	- `set QINIU_ACCESS_KEY=DraImie8qSbwoPoyrHV38lfTnVr9aj8S487egGsC`
	- `set QINIU_SECRET_KEY=62PYqPKVBwFIHvObxdrzeRcqB7Ep_ufpqTXLZ_8b`

- args
	- `-ak=DraImie8qSbwoPoyrHV38lfTnVr9aj8S487egGsC`
	- `-sk=62PYqPKVBwFIHvObxdrzeRcqB7Ep_ufpqTXLZ_8b`
	- `-domain=http://otzm88f21.bkt.clouddn.com`
	- `-bucket=document`
	- `-path=C:\Users\Yao\Desktop\76aaa869ly1fi6n1duxn5j21dw0kak5a.jpg`
