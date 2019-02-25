
# 概念

## 进程
- 操作系统中能独立运行、分配资源的基本单位
- = `程序`= `机器指令+ 数据+ 堆栈`

## 线程
- 独立运行、调度的基本单位
- 减少调度成本，提高并发性
- 同一进程的多个线程可共享进程的资源

## 协程
- 用户模式下，完全由应用程序控制的，轻量级线程
	- 拥有自己的寄存器、栈等结构
- 一个线程包含一或多个协程

## 纤程
- Windows 环境下，协程的叫法
- 线程一次只能执行一个纤程的代码

## 管程
- 用于管理临界资源，一次只且仅允许一个进程访问，互斥地控制多个进程的访问

## 并*
- 并行
	- 多个程序，同一时间只有一个运行
	- 多个队伍走同一个通道
- 并发
	- 多个程序同时运行
	- 多个队伍走多个通道


# 线程状态

## 描述

| 线程状态      | 描述                                        |
|---------------|-------------------------------------------|
| NEW           | 新建线程                                    |
| RUNNABLE      | 线程置入线程池，等待调度选中，获取 CPU 使用权 |
| BLOCKED       | 阻塞状态，等待锁资源                         |
| WAITING       | 等待状态，等待其它线程将其唤起               |
| TIMED_WAITING | 超时等待，等待休眠结束或其它关联线程结束     |
| TERMINATED    | 线程完成                                    |

## 流转

- 图示
	- ![Thread.State.png](http://doc.yqjdcyy.com/eac1bf44-78f0-49a7-ac41-0a4904520f3c.png)


# 串联
- thread
- cpu
- synchronized
- monitor
- volatite
 
# 基础

## 进程

- 调用
	- `Runtime`

		```java
		Process exec(*)
		```

	- `ProcessBuilder`

		```java
		new ProcessBuilder(...).start()
		```

- 底层
	- `ProcessImpl`
		- JVM.native method
	
		|  方法名   |         Windows API         |
		|-----------|-----------------------------|
		| create    | CreateProcess<br>CreatePipe |
		| close     | CloseHandle                 |
		| waitfor   | WaitForMultipleObject       |
		| destroy   | TerminateProcess            |
		| exitValue | GetExitCodeProcess          |

- 注意事项
	- 管道容量有限，需及时读取
		- 否则会导致进程挂起、死锁
	- windows 环境下调用，需要运行 windows 的命令解释器
		- `cmd /c start cmd.exe`
	- Linux 的管道操作 `|` 和 Windows 环境下的重定向 `>` 等均无法通过命令参数实现


## 线程

- 调用
	- `Thread`

		```java
		class YaoThread extends Thread {
		    @Override
		    public void run() {
		        System.out.println("Yao.Thread running");
		    }
		}
		```

	- `Runnable`

		```java
		class YaoRunnable implements Runnable {
		    @Override
		    public void run() {
		        System.out.println("Yao.Runnable running");
		    }
		}
		```

- 底层
	- `static {registerNatives();}` 以注册本地方法供 Thread 方法调用

		|       Native      |         调用方法         |
		|-------------------|--------------------------|
		| **start0**        | `&JVM_StartThread`       |
		| **stop0**         | `&JVM_StopThread`        |
		| isAlive           | `&JVM_IsThreadAlive`     |
		| suspend0          | `&JVM_SuspendThread`     |
		| resume0           | `&JVM_ResumeThread`      |
		| setPriority0      | `&JVM_SetThreadPriority` |
		| **yield**         | `&JVM_Yield`             |
		| **sleep**         | `&JVM_Sleep`             |
		| **currentThread** | `&JVM_CurrentThread`     |
		| countStackFrames  | `&JVM_CountStackFrames`  |
		| **interrupt0**    | `&JVM_Interrupt`         |
		| isInterrupted     | `&JVM_IsInterrupted`     |
		| holdsLock         | `&JVM_HoldsLock`         |
		| getThreads        | `&JVM_GetAllThreads`     |
		| dumpThreads       | `&JVM_DumpThreads`       |
	
	- 调用流程
		- `Thread.start`
			- `JVM_StartThread`
			- => 创建本地线程 `thread_entry`
				- `new JavaThread(&thread_entry, sz)`
    				- `vmSymbolHandles::run_method_name()`
    					- `template(run_method_name,"run")`
    					- => 调用`Thread.run()` 方法

	- java.lang.Thread 在系统本地线程的基础上，进行了封装
		- 但屏蔽了相关实现细节

			|     细项     |                                   描述                                  |
			|--------------|-------------------------------------------------------------------------|
			| 线程返回值   | Java 未提供<br>一般用于检测线程是否正常退出                             |
			| 同步         | `join()` 于循环调用时不可控                                             |
			| ID           | 自定义的计数 ID，与操作系统的线程 ID 无关                               |
			| 运行时间统计 | 统计值不准确<br>因多线程调度，无法准确获取线程的实际使用的 CPU 运算时间 |


# 工具

## ThreadLocal

## Atomic

## Lock

	Atomic


		原子更新
			基本类型
			数组
			引用
			字段.

		使用  Unsage 实现的包装类

	锁


	线程池




# 参考
- [进程 线程 协程 管程 纤程 概念对比理解](https://zhuanlan.zhihu.com/p/26757689)
- [Java 中的进程与线程](https://www.ibm.com/developerworks/cn/java/j-lo-processthread/index.html)
- []()
- []()
- []()
- []()
- []()
- []()