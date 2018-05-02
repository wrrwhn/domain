+++
date = "2018-05-02T16:45:00+08:00"
title = "Error.CSharp"
draft = false
tags = ["整理","Error","CSharp"]
share = true
+++

[TOC]


# 异常
## 在可以调用 OLE 之前，必须将当前线程设置为单线程单元(STA)模式，请确保您的Main函数带有STAThreadAttribute标记
### 解决
- Main 方法上增加 [STAThread] 标志
``` c#
Thread app = new Thread(new ParameterizedThreadStart(ShowWindow));
app.ApartmentState = ApartmentState.STA;

Thread newThread = new Thread(new ThreadStart(ThreadMethod));
newThread.SetApartmentState(ApartmentState.MTA);
```
### 解析
- `STA`
	- 线程创建并进入一**单线程**单元
- `MTA`
	- 线程创建并进入一**多线程**单元
- `Unknown`
	- 尚未设置 `ApartmentState` 属

### 过程
- `COM` 类中使用的公共语言库需要在 `COM interop` 情况下调用 `COM` 对象时创建单元
	- 其中托管进程帮忙创建时允许其进入一个线程的`单线程单元（STA）`或`多线程单元（MTA）`