---
title: "Oracle.常见问题"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "Oracle"
toc: true
---


## ORA-12541:TNS:无监听程序
- 监听失败，自己遇到的情况是主机设置错误
-进入到下述文件中： D:\oracle\product\10.2.0\db_2\network\ADMIN\listener.ora
  - (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.1.101)(PORT = 1521))
- 更新HOST为本地即 HOST= 127.0.0.1
- 重启监听服务


## ORA-01045 :user system lacks create session privilege; logon denied
- 由于新建的用户缺少机关的权限信息
- 用sys登入Oracle DB后,下grant create session to yqj;


## ORA-12514问题，即TNS 监听程序不能识别给定的SID
- 指定的数据库未加入监听
- 进入到下述文件中： D:\oracle\product\10.2.0\db_2\network\ADMIN\listener.ora
- 指定位置添加相关文字
```
 SID_LIST_LISTENER =
    (SID_LIST =
      (SID_DESC =
        (SID_NAME = PLSExtProc)
        (ORACLE_HOME = C:\oracle\product\10.2.0\db_1)
        (PROGRAM = extproc)
      )
  /**NEW
      (SID_DESC =
        (GLOBAL_DBANAME = testDB)
        (ORACLE_HOME = C:\oracle\product\10.2.0\db_1)
        (SID_NAME = testDB)
      )
  */
    )
  LISTENER =
    (DESCRIPTION_LIST =
      (DESCRIPTION =
        (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1))
        (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
      )
    )
```

## ORA-12518 TNS:监听程序无法分发客户机连接
- shutdown一半强制停止，之后建立对于用户登陆退出的触发器并进行登陆测试发现妕提示
- 自行解决方法
  + 重启服务
- 网上提供方法
  + connect sys/test as sysdba
  + show parameters dispatchers;
  + alter system set dispatchers = '(protocol=tcp)(dispatchers=3)(service=oracle10xdb)';



## EXP-00026
- CMD中执行下式：EXP YQJ/YQJ BUFFER=64000 FILE= D:\YQJ.DMP OWNER= YQJ TABLES=(STUDENT);
- 参数冲突


## ORACLE出现乱码
- 将语言和地域都先修改为英文之后再行修改回中文
  + ALTER SESSION SET NLS_LANGUAGE=american;
  + ALTER SESSION SET NLS_LANGUAGE='SIMPLIFIED CHINESE';
  + ALTER SESSION SET NLS_TERRITORY=america;
  + ALTER SESSION SET NLS_TERRITORY=CHINA;
