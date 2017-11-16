+++
date = "2017-08-09T22:07:46+08:00"
title = "Dump.Analyze"
draft = false
tags = ["整理","Dump"]
share = true
+++

## 使用
- 安装 Dump 包分析软件
- 安装完成后进行初始化设置
    - 设置【File/ Symbol file path】的属性值为【D:\MyCodesSymbols;SRV*c:\symbols*http://symbols.mozilla.org/firefox; SRV*D:\MyLocalSymbols*http://msdl.microsoft.com/download/symbols;C:\Windows\symbols】
- 编辑 vbs 文件，调整 windbg 执行目录
- 双击执行，并输入任务管理器中的指定监控软件的 PID 值
- 操作软件，并至崩溃状态（崩溃后会自动启动 windbg 进行 Dump 包存储）
- 开启 windbg x86 程序，并打开该 Dump 包（File/ Open Crash Dump）
- 待加载完成后输入【~* kp】显示异常堆栈
- 输入【!analyze -v -f】分析具体异常

## 资源
- [X86 Debuggers And Tools-x86_en-us.msi](http://otzm88f21.bkt.clouddn.com/X86%20Debuggers%20And%20Tools-x86_en-us.msi)
- [crashdump.vbs](http://otzm88f21.bkt.clouddn.com/crashdump.vbs)
- [命令集合.xlsx](http://otzm88f21.bkt.clouddn.com/%E5%91%BD%E4%BB%A4%E9%9B%86%E5%90%88.xlsx)
