+++
date = "2018-04-18T18:00:00+08:00"
title = "Java.Exception"
draft = false
tags = ["整理","Java"]
share = true
+++

[TOC]

# 分类
## Throwable
### Exception
- Check Exception
	- ReflectiveOperationException
		- ClassNotFoundException
		- NoSuchMethodException
		- NoSuchFieldException
		- IllegalAccessException
		- InstantiationException
	- CloneNotSupportedException
	- InterruptedException
		- ClassCastException
		- EnumConstantNotPresentException
		- IllegalStateException
		- NullPointException
		- IllegalMonitorStateException
		- IllegalArgumentException
			- IllegalThreadStateException
			- NumberFormatException
		- SecurityException
- UnCheck Exception
	- RuntimeException
		- ArithmeticException
		- TypeNotPresentException
		- IndexOutOfBoundsException
			- ArrayIndexoutOfBoundsExxception
			- StringIndexoutOfBoundsException
		- NegativeArraySizeException
		- ArrayStoreException
		- UnsupportedOperationException
### Error
- VirtualMachineError
	- InternalError
	- StackOverflowError
	- UnknownError
	- OutOfMemoryError
- ThreadDeath
- AssertionError
- LinkageError
	- UnsatisfiedLinkError
	- NoClassDefFoundError
	- VerifyError
	- IncompatibleClassChangeError
		- InstantiationError
		- NoSuchFieldError
		- NoSuchMethodError
		- AbstractMethodError
		- IllegalAcessError
	- BootstrapMethodError
	- ExceptionInInitializerError
	- ClassFormatError
		- UnsupportedClassVersionError
	- ClassCircularityError


# 明细
## Throwable
- 简介
	- 所有错误和异常的基类
	- 仅该类实例可被 JVM 抛出或于声明中抛出
	- 设计中包含 cause 的原因
		- 底层定义异常类型，顶层描述报错位置和具体异常原因
		- 设计上提醒开发者注意异常，并进行捕获处理，而非直接抛出
- 方法
	- Throwable(String message)
	- getMessage()
		- 明细消息
	- getLocalizedMessage()
		- 局部描述，子类重写该方法
	- toString()	
	- printStackTrace()
	- printStackTrace(PrintStream s)

## Exception
- 描述
	- 分类
		- 检查型异常
			- 编译器要求程序必须捕获或声明抛出异常
		- 非检查型异常 | 运行时异常

- 方法
	- Exception()
	- Exception(String message)


## Error
- 描述
	- 编译时错误 | 系统错误
- 方法
	- Error()
	- Error(String message)