---
title: "Go.VSCode"
date: "2017-07-31"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---


# VS-Code Go 开发
## 参考
- [VS Code 搭建 Go 开发IDE](https://www.wonsikin.me/2016/06/06/VS-Code-%E6%90%AD%E5%BB%BA-Go-%E5%BC%80%E5%8F%91IDE/)
- [Running VS Code on Windows](https://code.visualstudio.com/docs/setup/windows)

## 安装
- 安装 vs-code
- 「Ctrl+ Shift+ P」后输入 「install Extensions」「Go」
- 安装「lukehoban - Go」 版本

## 调试
- 切换到「调试」界面，并点击「打开 launch.json」打开配置
- 调整指定配置项
```
<!-- 当前项目于 GOPATH 中的路径 -->
"program": "${workspaceRoot}\\Utils_Go\\src\\md5",
<!--设置当前环境变量 -->
"env": {
"GOPATH": "D:\\server\\go\\lib;D:\\work\\git\\yao\\go\\Hello_Go;D:\\work\\git\\yk\\go\\pptconverter-gateway\\code;${workspaceRoot}\\Utils_Go"
},
<!--设置启动参数 -->
"args": [
"-c=1",
"-p=D:\\work\\git\\yao\\go\\Utils_Go\\template\\dest.mp4"
],
```
- 设置断点、进行调试

## 注
- 安装「lukehoban - Go」时，部分「go get」部分来源需要`翻墙`
- 调试相关快捷键同 `visual stuio`
