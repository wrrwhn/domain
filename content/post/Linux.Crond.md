
# Linux.Crontab

## 参考
- [每天一个linux命令（50）：crontab命令](http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)


## 简介
- 用来周期性的执行某种任务或等待处理某些事件的一个守护进程
- 系统任务调度
	- 系统周期性所要执行的工作，比如写缓存数据到硬盘、日志清理等
	- /etec/crontab
		```
		SHELL=/bin/bash								# 指定 Shell 版本
		PATH=/sbin:/bin:/usr/sbin:/usr/bin			# 指定系统执行命令的路径
		MAILTO=""									# 指定 crond 任务执行消息将通过指定邮件发送给 root 用户
		HOME=/										# 指定在执行命令谨脚本时使用的主目录

		# run-parts
		51 * * * * root run-parts /etc/cron.hourly
		24 7 * * * root run-parts /etc/cron.daily
		```
- 用户任务调度
	- 用户定期要执行的工作，比如用户数据备份、定时邮件提醒等
	- /var/spool/cron


## 配置
- `/etc/cron.deny`
    - 列举不允许使用 `crontab` 命令的用户
- `/etc/cron.allow`
    - 列举允许使用 `crontab` 命令的用户
- `/var/spool/cron/`
    - 所有用户 `crontab` 文件存放的目录


## 格式
- `minute hour day month week command`
    - 字段
    	- minute 
    		- 0- 59
    	- hour 
    		- 0- 23
    	- day 
    		- 1- 31
    	- month 
    		- 1- 12
    	- week 
    		- 0- 7
    			- 0和 7 均代表星期天
    	- command
    - 可选项
    	- `*`
    		- 所有可能值
    	- `,`
    		- 值列表，如 `1,2,5,7`
    	- `-`
    		- 值范围，如 `2-6`
    	- `/`
    		- 指定间隔频率，如 `*/10`，表示每隔十分钟执行一次
    - 说明
    	- ![08090352-4e0aa3fe4f404b3491df384758229be1.png](http://otzm88f21.bkt.clouddn.com/c3a7e01f-b2e9-4f4e-beb0-81ed2bc21d1c.png)


## 服务
- `/sbin/service crond [start|stop|restart|reload|status]`
- `ntsysv`
    - 查看服务是否设置为开机启动
- `chkconfig -level 35 crond on`
    - 将 crond 加入开机自动启动
- `ntsysv | chkconfig --list`
    - 查看 crond 是否已添加至开机启动
- `chkconfig –level 35 crond on`
    - 将 crond 添加至开机启动
    - 本地**尝试无果**


## 格式
- `crontab [-u user] file`
- `crontab [-u user] [-l | -r | -e] [-i] [-s]`
- `crontab -n [ hostname ]`
- `crontab -c`


## 参数
- `-u <user>`
    - 调用指定用户 `<user>` 名下的定时任务
- `-l`
    - 于标准输出流中展示当前的**定时任务列表**
- `-r`
    - **移除**当前定时任务
- `-e`
    - 以 `视图` 或 `编辑器` 编辑，并于离开编辑器时，修改的定时任务将自动被安装
- `-i`
    - 配合 `-r` 使用，用于删除前的提示，避免误操作
- `-s`
    - 于定时任务编辑或替换发生前，追加安全内容字符串作为 `MLS_LEVEL` 设置
- `-n`
    - 配合 `-c` 使用，支持集群
    - 设置集群主机，用于运行 `/var/spool/cron` 下的定时任务
    - 若提供主机名被匹配，将被选择运行指定的定时任务；若无主机匹配，则定时任务不再运行；而若主机名被忽略，则当前主机升级为默认主机
    - 参数不影响 `/etc/crontab` 和 `/etc/cron.d` 中的定时任务的运行
- `-c`
    - 允许支持集群，用于查询当前集群中哪台主机用于执行 `/var/spool/cron` 中的定时任务


## 事例



## 异常
- `no crontab for root`
	- `crontab -l` 时触发，因为该用户第一次使用，尚未生成对应文件导致
	- 可使用 `crontab -e` 后保存以解决