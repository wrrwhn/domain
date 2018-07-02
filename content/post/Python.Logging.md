---
title: "Python.Logging"
date: "2018-04-11"
categories:
 - "整理"
tags:
 - "Python"
toc: true
---


# 组成
## Logger
- 作用
	- 日志对外暴露接口
- 创建
	- `logger = logging.getLogger(logger_name)`
## Handler
- 作用
	- 将日志内容转至相应文件
- 类型
	- StreamHandler
		- `sh = logging.StreamHandler(stream=None)`
	- FileHandler
		- `fh = logging.FileHandler(filename, mode='a', encoding=None, delay=False)`
	- NullHandler
## Filter
- 作用
	- 将日志内容进行过滤
## Formater
- 作用
	- 控制日志输出格式
- LogRecord.对象
	- `name`
		- 日志记录器名称
		- 不会因 Handler 将其转到其它 Logger 而改变
	- `level`
		- 日志级别名称
		- 如`INFO`、`ERROR`
	- `pathname`
		- 源文件的完整路径名称
	- `lineno`
		- 调用处在源文件中行号
	- `msg`
		- 日志消息体
	- `args`
		- 参数+ 日志消息体
	- `exc_info`
		- 当前异常信息
	- `func`
		- 调用处所在的方法体名称
- LogRecord.参数

	|    Attribute    |        Format       |                          Description                          |
	|-----------------|---------------------|---------------------------------------------------------------|
	| asctime         | %(asctime)s         | LogRecord创建时间的人为识别格式<br/>如2003-07-08 16:49:45,896 |
	| created         | %(created)f         | LogRecord创建时间的时间戳格式                                 |
	| msecs           | %(msecs)d           | LogRecord创建时间的毫秒部分                                   |
	| relativeCreated | %(relativeCreated)d | LogRecord的已创建时长                                         |
	| pathname        | %(pathname)s        | 源文件的完整路径名                                            |
	| filename        | %(filename)s        | `pathname`的文件名部分                                        |
	| module          | %(module)s          | `filename` 的部分                                             |
	| funcName        | %(funcName)s        | 调用方法名                                                    |
	| levelname       | %(levelname)s       | 'DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL'               |
	| levelno         | %(levelno)s         | `levelname` 的对应数值                                        |
	| lineno          | %(lineno)d          | 源文件中报错行所在行的号码                                    |
	| message         | %(message)s         | LogRecord.msg & LogRecord.args                                |
	| name            | %(name)s            | 所使用的日志记录器名称                                        |
	| process         | %(process)d         | 进程 ID                                                       |
	| processName     | %(processName)d     | 进程名称                                                      |
	| thread          | %(thread)d          | 线程 ID                                                       |
	| threadName      | %(threadName)s      | 线程名称                                                      |

- 示例
	- `logging.basicConfig(format="%(asctime)-15s %(clientip)s %(user)-8s %(message)s")`
	

# 补充

## 日志级别
- 明细

	|  Level   |                Usage                | Value |
	|----------|-------------------------------------|-------|
	| NOTSET   |                                     |     0 |
	| DEBUG    | 调试时的信息输出                    |    10 |
	| INFO     | 运行状态、信息输出                  |    20 |
	| WARNING  | 预警消息<br/>如磁盘将满，费用不足等 |    30 |
	| ERROR    | 运行时相关异常消息                  |    40 |
	| CRITICAL | 运行失败消息                        |    50 |


## 配置方法
- 默认配置
- 简单配置
	- `logging.basicConfig(filename='20180411.log', level=logging.INFO,
                        format='%(asctime)-15s %(levelname)s %(filename)s \t%(lineno)d \t%(message)s')`
- 文件配置
	- `logging.config.fileConfig("path")`
- 字典配置
	- `logging.config.dictConfig(config)`
- 网络配置
	- 略

## 流程
- ![logging_flow.png](http://otzm88f21.bkt.clouddn.com/be92d65e-86d8-49e3-be7c-296e02426a99.png)


# 示例
## 代码配置
- Code
	```
	import logging

	logger = logging.getLogger('example')
	logger.setLevel(logging.DEBUG)

	ch = logging.StreamHandler()
	ch.setLevel(logging.DEBUG)
	formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
	ch.setFormatter(formatter)
	logger.addHandler(ch)

	logger.debug('debug message')
	logger.info('info message')
	logger.warn('warn message')
	logger.error('error message')
	logger.critical('critical message')
	```
- Output
	```
	2005-03-19 15:10:26,618 - example - DEBUG - debug message
	2005-03-19 15:10:26,620 - example - INFO - info message
	2005-03-19 15:10:26,695 - example - WARNING - warn message
	2005-03-19 15:10:26,697 - example - ERROR - error message
	2005-03-19 15:10:26,773 - example - CRITICAL - critical message
	```

## 配置文件
- Config
	- logging.conf
	```
	[loggers]
	keys=root,user

	[handlers]
	keys=consoleHandler,fileHandler

	[formatters]
	keys=formatter

	[logger_root]
	level=WARN
	handlers=consoleHandler

	[logger_user]
	level=DEBUG
	handlers=fileHandler
	qualname=user
	propagate=0

	[handler_consoleHandler]
	class=StreamHandler
	level=DEBUG
	formatter=formatter
	args=(sys.stdout,)

	[handler_fileHandler]
	class=FileHandler
	level=DEBUG
	formatter=formatter
	args=('file-conf.log',)

	[formatter_formatter]
	format=%(asctime)s\t%(levelname)s\t%(message)s
	datefmt=
	```

	- logging.yqml
	```
	version: 1
	formatters:
	  simple:
	    format: '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
	handlers:
	  console:
	    class: logging.StreamHandler
	    level: DEBUG
	    formatter: simple
	    stream: ext://sys.stdout
	loggers:
	  simpleExample:
	    level: DEBUG
	    handlers: [console]
	    propagate: no
	root:
	  level: DEBUG
	  handlers: [console]
	```

- Code
	```
	import logging
	import logging.config

	logging.config.fileConfig("logging.conf")

	default = logging.getLogger()
	default.debug("File\tDefault\tdebug")
	default.info("File\tDefault\tinfo")
	default.warn("File\tDefault\twarn")
	default.error("File\tDefault\terror")
	default.critical("File\tDefault\tcritical")

	user = logging.getLogger("user")
	user.debug("File\tUser\tdebug")
	user.info("File\tUser\tinfo")
	user.warn("File\tUser\twarn")
	user.error("File\tUser\terror")
	user.critical("File\tUser\tcritical")
	```

- Output
	- 同`代码配置`模块输出

## 对象配置
- Code
	```
	import logging
	import logging.config

	DEFAULT_LOGGING = {
	    'version': 1,
	    'disable_existing_loggers': False,
	    'loggers': {
	        '': {
	            'level': 'INFO',
	        },
	        'another.module': {
	            'level': 'DEBUG',
	        },
	    }
	}

	logging.config.dictConfig(DEFAULT_LOGGING)
	logging.info('Hello, log')
	```

- Detail
	```
	{ 
	    'version': 1,
	    'disable_existing_loggers': False,
	    'formatters': { 
	        'standard': { 
	            'format': '%(asctime)s [%(levelname)s] %(name)s: %(message)s'
	        },
	    },
	    'handlers': { 
	        'default': { 
	            'level': 'INFO',
	            'formatter': 'standard',
	            'class': 'logging.StreamHandler',
	        },
	    },
	    'loggers': { 
	        '': { 
	            'handlers': ['default'],
	            'level': 'INFO',
	            'propagate': True
	        },
	        'django.request': { 
	            'handlers': ['default'],
	            'level': 'WARN',
	            'propagate': False
	        },
	    } 
	}
	```


# 参考
## 官方
- [Logging HOWTO](https://docs.python.org/2/howto/logging.html)
- [Formatter Objects](https://docs.python.org/2/library/logging.html#formatter-objects)
- [LogRecord attributes](https://docs.python.org/2/library/logging.html#logrecord-attributes)

## 整理
- [python logging模块使用教程](https://www.jianshu.com/p/feb86c06c4f4)
- [日志（Logging）](http://pythonguidecn.readthedocs.io/zh/latest/writing/logging.html)

## 配置
- [Configuration file format](https://docs.python.org/2.4/lib/logging-config-fileformat.html)
- [How to use logging with python's fileConfig](https://stackoverflow.com/questions/13649664/how-to-use-logging-with-pythons-fileconfig-and-configure-the-logfile-filename)

## YAML
- [官网](http://pyyaml.org/wiki/PyYAML)
- [How to install pyYAML on windows 10](https://stackoverflow.com/questions/33665181/how-to-install-pyyaml-on-windows-10)
- [Unofficial Windows Binaries for Python Extension Packages](https://www.lfd.uci.edu/~gohlke/pythonlibs/#pyyaml)
