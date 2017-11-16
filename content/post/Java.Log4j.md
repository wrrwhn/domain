+++
date = "2017-11-15T22:07:46+08:00"
title = "Java.Log4j"
draft = false
tags = ["整理","Java "]
share = true
+++


## 文件格式
### 配置根Logger
- log4j.rootLogger  =   [ level ]   ,  appenderName1 ,  appenderName2 ,  …

### 配置日志信息输出目的地
```
log4j.appender.appenderName  =  Appender
log4j.appender.appenderName. Threshold=  DEBUG        ##输出DEBUG级别以上的日志
…
log4j.appender.appenderName.optionN  =  valueN
```

### 配置日志信息的格式（布局）
```
log4j.appender.appenderName.layout  =  fully.qualified.name.of.layout.class
log4j.appender.appenderName.layout.ConversionPattern =  value1
…
log4j.appender.appenderName.layout.optionN  =  valueN
```

## 参数
### [ level ]  - 日志输出级别
- FATAL[0]
- ERROR[3]
- WARN[4]
- INFO[6]
- DEBUG[7]

### Appender - 日志输出目的地
- org.apache.log4j.ConsoleAppender（控制台）
- org.apache.log4j.FileAppender（文件）
- org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件）
- org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件）
- org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）

### Layout  - 日志输出格式
- org.apache.log4j.HTMLLayout（以HTML表格形式布局）
- org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
- org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
- org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）

### ConversionPattern - 打印参数
- `%m`    输出代码中指定的消息
- `%p`    输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL
- `%r`    输出自应用启动到输出该log信息耗费的毫秒数
- `%c`    输出所属的类目，通常就是所在类的全名
- `%t`    输出产生该日志事件的线程名
- `%n`    输出一个回车换行符，Windows平台为“\r\n”，Unix平台为“\n”
- `%d`    输出日志时间点的日期或时间，默认格式为ISO8601，亦指定格式，比如：%d{yyy MMM dd HH:mm:ss , SSS}，输出类似：2002年10月18日  22 :10:28 ， 921 
- `%l`    输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数。举例：Testlog4.main(TestLog4.java: 10 )



