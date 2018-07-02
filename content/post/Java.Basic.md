---
title: "Java.Basic"
date: "2017-08-09"
categories:
 - "整理"
tags:
 - "Java"
toc: true
---


# 参考
- [使用 JDK 5 后的线程并发，Callable, Future, ExecutorServie](http://unmi.cc/jdk-5-concurrent-callable-futuretask-etc/)
- [Lock(重入锁，读写锁)及Condition示例](http://www.cnblogs.com/wn398/archive/2013/06/15/3130967.html)
- [Java中的锁](http://ifeve.com/locks/)


# 功能
## 获取路径
### 分类
- 绝对路径
    - 主页上的文件或目录在硬盘上真正的路径，如URL和物理路径
    - 例
        - `C:xyz est.txt`
            - 代表了test.txt文件的绝对路径
- 相对路径
    - 相对与某个基准目录（一般对照WEB）的路径。
    - 示例
        - `"/"代表Web应用的跟目录`
        - `"./" 代表当前目录`
        - `"../"代表上级目录`
- 注
    - JSP/SERVLET中的比较特殊，服务器端的地址服务器端的相对地址指的是相对于你的web应用的地址，因为该地址是由服务器端解析聘的

### 获取方法
#### JSP路径/系统全路径
- 使用环境
    - SERVLET/JSP
- 使用语句
    - `request.getRealPath("？") --> ？为/  .  空格  web.xml`
    - `request.getRealPath(request.getRequestURI());`
    - `getServletContext().getRealPath("\")     //站点绝对路径`
    - `application.getRealPath("")    //JSP界面使用`
    - `ServletContext().getRealPath("")`
- 返回字串
    - `C:\Apache\Tomcat\webapps\local+ （\  \.  空格  \web.xml）`

#### 工程Classes下路径（SRC/CLASS）
- 使用环境
    - SERVLET/JSP/JAVA（即任意CLASS）
- 使用语句
    - `this.getClass()/JdomParse.class .getClassLoader().getResource("").getPath(); `       
        - 因为三者均为JAVA程序，都为CLASS，故为三者通用方法
    - `this.getClass()JdomParse.class .getResource("").getPath().toString();`
        - 可用于不同WEB环境来确认路径
    - `Thread.currentThread().getContextClassLoader().getResource("").getPath()`
- 返回字串
    - `/D:/workspace/strutsTest/WebRoot/WEB-INF/classes/`
    - `/D:/workspace/strutsTest/WebRoot/WEB-INF/classes/bl/`
    - `/E:/order/002_ext/WebRoot/WEB-INF/classes/`

#### 系统路径
- 使用环境
    - APPLICATION/SERVLET/JSP
- 使用语句
    - `System.getProperty("user.dir")`
        - 相对项目（JAVA为项目，WEB依工具而定）路径
    - `ServletContext servletContext = config.getServletContext(); String rootPath = servletContext.getRealPath("/"); `
    - `application.getRealPath("")`
- 返回字串


#### WEB根上下文环境（即相对路径）
- 使用环境
    - 于SERVLET的INIT中
    - 于httpServletRequest中
- 使用语句
    - `request.getContextPath()getRealPath("/");`
    - `request.getSession().getServletContext().getRealPath("/");`
- 返回字串
    - `D:\工具\Tomcat-6.0\webapps\002_ext\ （其中002_ext为项目名称）`
    - `request.getContextPath() -> web项目名`

#### 站点虚拟路径   
- 使用环境
- 使用语句
    - getContextPath():
- 返回字串


#### 类加载器路径
- 使用环境
- 使用语句
    - `class.getClassLoader.getPath()；`
        - `InputStream is=TestAction.class.getClassLoader().getResourceAsStream("test.txt");`
        - `InputStream is=Test1.class.getResourceAsStream("/test.txt");`
- 返回字串
    - 均返回指定路径文件（\src\test.txt）的装载项目
- 注意
    - 不论是一般的java项目还是web项目,先定位到能看到包路径的第一级目录（TestAction在其中）


## 泛型
### 介绍
- 重复代码重构，于编译时检测类型安全，保存所有强制类型均为自动或隐式，提高代码重用率
### 规则
- 明细
    - 泛型参数允许多个，且仅能为类类型，不能为简单类型
    - 泛型参数支持extends(有办类型)或通配符类型
    - 支持类型有泛型类、接口和方法
- 实例
    - 泛型
        ```
        class Gen<T> {
          private T ob; //定义泛型成员变量
          public Gen(T ob) {
              this.ob = ob;
          }
          public T getOb() {
              return ob;
          }
          public void setOb(T ob) {
              this.ob = ob;
          }
          public void showType() {
              System.out.println("T的实际类型是: " + ob.getClass().getName());
          }
        }
        public class GenDemo {
          public static void main(String[] args){
              //定义泛型类Gen的一个Integer版本
              Gen<Integer> intOb=new Gen<Integer>(88);
              intOb.showType();
              int i= intOb.getOb();
              System.out.println("value= " + i);

              //定义泛型类Gen的一个String版本
              Gen<String> strOb=new Gen<String>("Hello Gen!");
              strOb.showType();
              String s=strOb.getOb();
              System.out.println("value= " + s);
          }
        }
        ```
    - 泛型方法
        ```
        public static <T> T display(T t) {
            return t;
        }
        ```

## JMX
### 参考
- [介绍](http://www.ibm.com/developerworks/cn/java/j-lo-jse63/)
- [Tomcat配置](http://tomcat.apache.org/tomcat-7.0-doc/monitoring.html#Enabling_JMX_Remote)
- [入门示例](http://rabbit9898.iteye.com/blog/1009198)
- [官方事例](http://docs.oracle.com/javase/1.5.0/docs/guide/jmx/examples.html)
- [官方教程](http://docs.oracle.com/javase/tutorial/jmx/index.html)
- [Using JConsole](http://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html)


### 组成
- Instrumentation
    - 使用MBean在遵循JMX规范中定义的设计模式和接口的基础上，确保提供标准化管理资源的仪表。
    - MXBean为在MBean的基础上预定义了一组数据类型。
- JMX agent
    - 直接操作资源并使之于远程应用上生效，其核心部件为MBean server。
- Remote management
    - 通过协议适配器和连接器支持JVM提供外部JMX agent


### 监控管理
#### 平台MXBeans和平台MBean Server
- 平台MXBeans
    - 用监视和管理JVM及运行时环境的的组件，功能包括类加载系统，即时编译系统和垃圾回收等平台。并保证不同环境下的JVM使用统一规范。
- 平台MBean Server
    - 用于注册管理平台MXBeans或自行创建的MBeans。

#### JConsole
- 介绍
    - java自带(JAVA_HOME/bin/jconsole.exe)可视化监控管理工具，可查看Java平台上运行的程序的性能和资源消耗的信息。
    - 监控本地和远程JVM、java应用程序的可视化监督工具，使用JMX技术实现。
- 推荐
    - 使用指导
        - 参见参考中的「Using JConsole」
- 异常
    - 描述
        - jConsole的MXBean树显示不合理
    - 事例
        ```
        MXBean列表
            com.sun.example:type=Triangle,side=isosceles,name=1
            com.sun.example:type=Triangle,name=2,side=isosceles
            com.sun.example:type=Triangle,side=isosceles,name=3
        树形结构
            |-com.sum.example
                |-Triangle
                    |-isosceles
                        |-1
                        |-3
                    |-2
                        |-isosceles
        ```
    - 解决
        - 补充-Dcom.sun.tools.jconsole.mbeans.keyPropertyList=type,side,name，指定关键字的顺序


#### MBeans介绍
- 概念
    - 类似于JavaBean，并可管理所有需要管理的应用、资源和设备。
- 组成
    - 一系列可读可写属性
    - 一系列可调用方法
    - 自行描述
- 分类
    - Standard MBeans
    - Dynamic MBeans
    - Open MBeans
    - Model MBeans
    - MXBeans
- 实例
    - 标准MBean= todoMBean接口（定义所有方法属性）+ todo实现
        ```
        public interface HelloMBean {   
            // todoMBean接口
            // 规范要求接口必须以 dosome+ MBean为名称
            public String getName();        //只读
            public int getCacheSize();      //可读可写
            public void setCacheSize(int size);
        }
        public class Hello implements HelloMBean {      //todo实现
            private final String name = "Reginald";
            private int cacheSize = DEFAULT_CACHE_SIZE;
            private static final int DEFAULT_CACHE_SIZE = 200;

            public String getName() {
                return this.name;
            }                   
            public int getCacheSize() {
                return this.cacheSize;
            }
            public synchronized void setCacheSize(int size) {
                this.cacheSize = size;
                System.out.println("Cache size now " + this.cacheSize);
            }
        }
        ```

    - MXBean= 可支持任意客户端（包括远程）。
        - 命名不一定要为todoMXBean，可使用@MXBean注解
        ```
        public interface QueueSamplerMXBean {       //MXBean接口
            public QueueSample getQueueSample();
            public void clearQueue();
        }
        public class QueueSampler implements QueueSamplerMXBean {       //定义QueueSamplerMXBean实现
            private Queue<String> queue;

            public QueueSampler (Queue<String> queue) {
                this.queue = queue;
            }
            public QueueSample getQueueSample() {               //自定义返回类型
                synchronized (queue) {
                    return new QueueSample(new Date(), queue.size(), queue.peek());
                }
            }                       
            public void clearQueue() {
                synchronized (queue) {
                    queue.clear();
                }
            }
        }
        ```

    - 通知
        - 用于反馈状态改变、事件或问题异常
        - 必须实现 NotificationEmitter 或继承 NotificationBroadcasterSupport
        - 实现 Notification 或子类如 AttributeChangedNotification
            ```
            public class Hello extends NotificationBroadcasterSupport implements HelloMBean {

                public synchronized void setCacheSize(int size) {
                    Notification n = new AttributeChangeNotification(this, sequenceNumber++, System.currentTimeMillis(), "CacheSize changed", "CacheSize", "int", oldSize, this.cacheSize);
                    sendNotification(n);
                }

                @Override
                public MBeanNotificationInfo[] getNotificationInfo() {
                    String[] types = new String[]{
                        AttributeChangeNotification.ATTRIBUTE_CHANGE
                    };

                    String name = AttributeChangeNotification.class.getName();
                    String description = "An attribute of this MBean has changed";
                    MBeanNotificationInfo info =
                            new MBeanNotificationInfo(types, name, description);
                    return new MBeanNotificationInfo[]{info};
                }
            }
            ```

#### 远程管理
- 概念
    - JMX API 允许通过JMX连接器（服务端和客户端组成）远程管理个人资源，并定义了远程方法调用（RMI）基础上标准连接协议。
    - 需要应用配置以正常的参数，具体可参见【Tomcat本地JMX监控】或如下例
- 指令调用
    - javac com/example/*.java    //编译java文件
    - java -Dcom.sun.management.jmxremote.port = 9999  \
         - -Dcom.sun.management.jmxremote.authenticate = false \
         - -Dcom.sun.management.jmxremote.ssl = false \
         - com.example.Main       //启动时配置调用端口等信息               
- 启动要求
    - java1.5
        - 命令行指定JMX才会启动。
    - java1.6
        - 默认启动JMX。
    - 注
        - jConsole可通过pid（进程ID）进行JMX管理，其中内部将pid转换为JMX URL。
- 调用
    ```
    //RMI+ 连接+ MBeanServer获取
        JMXServiceURL url =new JMXServiceURL("service:jmx:rmi:///jndi/rmi://:9999/jmxrmi");
        JMXConnector jmxc = JMXConnectorFactory.connect(url, null);    
        MBeanServerConnection mbsc = jmxc;
    //明确的MBean或MXBean - 代理调用
        ObjectName mbeanName = new ObjectName("com.example:type=Hello");
        HelloMBean mbeanProxy = JMX.newMBeanProxy(mbsc, mbeanName, HelloMBean.class, true);
            或 OperatingSystemMXBean osBean= ManagementFactory.newPlatformMXBeanProxy(mbsc, ManagementFactory.OPERATING_SYSTEM_MXBEAN_NAME, OperatingSystemMXBean.class);
        mbeanProxy.getCacheSize();      //调用方法和属性
    //未明确MBean或MXBean情况
        ObjectName objectName= new ObjectName(appName+ ":name=MuleContext");        //获取属性
        String status= mbsc.getAttribute(objectName, "Stopped").toString();
        mbsc.invoke(object, action, param, signature);                              //调用方法
    ```
- 连接方式整理
    - MBeanServerFactory.createMBeanServer();
        ```
        //ManagementFactory.getPlatformMBeanServer()第一次调用时会默认调用上述方法
        ObjectName name = new ObjectName("book.liuyang:service=Counter");
        server.registerMBean(new Counter(), name);
            // 可见Domain为JMImplementation
        ```
    - getMBeanServerConnection()
        ```
        JMXConnectorFactory.connect(
            new JMXServiceURL("service:jmx:rmi:///jndi/rmi://:9999/jmxrmi"),
            null
        ).getMBeanServerConnection();
        ```
        - 可见Domain为 `JMImplementation,com.sun.management,Catalina,java.nio,org.apache.commons.pool2,java.lang,java.util.logging`

#### JVM运行情况 - JMX
- 监控双方于同一JVM
    - `MBeanServer server = ManagementFactory.getPlatformMBeanServer();  `
    - `RuntimeMXBean rmxb = ManagementFactory.newPlatformMXBeanProxy(server, "java.lang:type=Runtime", RuntimeMXBean.class);  `
- 监控双方位于不同JVM
    - 被监控JVM补充JVM代码启动参数
        - `-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=127.0.0.1:8000 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false`
    - 连接代理
        - `JMXServiceURL url = new JMXServiceURL("service:jmx:rmi:///jndi/rmi://127.0.0.1:8000/jmxrmi");  `
        - `JMXConnector connector = JMXConnectorFactory.connect(url);  `
        - `RuntimeMXBean rmxb = ManagementFactory.newPlatformMXBeanProxy(connector.getMBeanServerConnection(),"java.lang:type=Runtime", RuntimeMXBean.class);`
- 监控双方位于不同JVM，但处于同一物理主机(Java Instrutment& Attach API)
    - 通过Attach到被监控的JVM进程，并在被监控的JVM中启动一个JMX代理，然后使用该代理通过2的方式连接到被监控的JVM的JMX上。
        ```
        //Attach 到5656的JVM进程上，后续Attach API再讲解 
        VirtualMachine virtualmachine = VirtualMachine.attach("5656"); 

        //让JVM加载jmx Agent，后续讲到Java Instrutment再讲解 
        String javaHome = virtualmachine.getSystemProperties().getProperty("java.home"); 
        String jmxAgent = javaHome + File.separator + "lib" + File.separator + "management-agent.jar"; 
        virtualmachine.loadAgent(jmxAgent, "com.sun.management.jmxremote"); 

        //获得连接地址 
        Properties properties = virtualmachine.getAgentProperties(); 
        String address = (String)properties.get("com.sun.management.jmxremote.localConnectorAddress"); 

        //Detach 
        virtualmachine.detach(); 

        JMXServiceURL url = new JMXServiceURL(address); 
        JMXConnector connector = JMXConnectorFactory.connect(url); 
        RuntimeMXBean rmxb = ManagementFactory.newPlatformMXBeanProxy(connector.getMBeanServerConnection(), "java.lang:type=Runtime",RuntimeMXBean.class); 
        ```



#### TOMCAT本地JMX监控
- 参考
    - http://sharpspeed.iteye.com/blog/2009770
- windows
    - 系统配置
        - windows7 64位
        - java 1.7.0_55
        - tomcat 7.0.54
    - 配置修改
        - D:\Program Files\Apache\Tomcat\apache-tomcat-7.0.54\bin\catalina.bat
        - 于:doRun节点下补充
            - `set JMX_REMOTE_CONFIG=-Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.local.only=true -Dcom.sun.management.jmxremote.port=9999 - -Dcom.sun.management.jmxremote.ssl=false``-Dcom.sun.management.jmxremote.authenticate=false`
            - `set CATALINA_OPTS=%CATALINA_OPTS% %JMX_REMOTE_CONFIG%`
        - 其中port为代码连接端口，与jConsole中列举的端口不一致
        - 若需要鉴权，则请将authenticate设置为true，并调整鉴权相同配置，具体可见参考内容
    - 连接测试
        - `String rmi= "service:jmx:rmi:///jndi/rmi://localhost:9999/jmxrmi";`
        - `JMXServiceURL serviceURL = new JMXServiceURL(rmi);`
        - `conn = JMXConnectorFactory.connect(serviceURL);`
        - `conn.getMBeanServerConnection();`
- Linux
    - 系统配置
        - CentOS 6.5
        - java 1.6.0_33
        - tomcat 7.0.54Z
    - 配置修改（含tomcat安装）
        - `cd /data/test/download/targz`
        - `rz #上传apache-tomcat-7.0.54.tar.gz`
        - `tar -xzf apache-tomcat-7.0.54.tar.gz`
        - `cd apache-tomcat-7.0.54/bin`
        - `vi catalina.sh`
        - `/Execute The Requested Command`
        - 于当行后补充 `CATALINA_OPTS="$CATALINA_OPTS -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=9999 -Dcom.sun.management.jmxremote.ssl=false -Dcom.sun.management.jmxremote.authenticate=false"`
            - 且注意上例代码不能折行！！
        - `ESC`
        - `:wq`
        - `./startup.sh`
        - `rz #上传测试执行文件jmx.jar`
    - 连接测试
        - `java -jar /data/test/download/targz/apache-tomcat-7.0.54/bin/jmx.jar`


## 多线程
### 创建方法
- 继续Thread
    ```
    public class ExtendThread extends Thread {
        public void run(){System.out.println(this.getName());}
    }
    new ExtendThread().start();
    ```
- 实现Runnable
    ```
    public class ImpleRunnable implements Runnable {
        public void run(){System.out.println(this.getName());}
    }
    new Thread(new ImpleRunnable(),"Thread").start(); 
    ```
- 注意
    - Runable非常适合多个相同线程来处理同一份资源的情况

### 生命周期
- New新建
    - 当线程被创建时，该线程处于新建状态，此时它和其他java对象一样，仅仅由Java虚拟机为其分配了内存，并初始化了其成员变量的值。（此时的线程没有表现出任何表现出任何线程的动态特征，程序也不会执行线程的线程执行体）
    - new Thread（）||new Thread（Runnable target，String name）。
- Runnable就绪
    - 就绪也就是说启动线程，调用start方法来启动线程，系统会将该run方法当成线程执行体来处理。如果直接调用线程对象的run方法。则run方法会立即执行，且在这个run方法的执行体未执行结束前其他线程无法并发执行（即系统会将run方法当做一个普通对- 象的普通方法，而不是线程执行体对待）
        - 如果有一个主线程，一个子线程。当根据逻辑代码该调用子线程时不一定会立即调用，为了想在子线程start（）后立即调用子线程，可以考虑使用Thread.sleep（1），这样会让当前线程（主线程）睡眠1毫秒，因为cpu在这1毫秒中是不会休息的，- 这样就会去执行一条处于就绪状态的线程。
        - 不能对已经处于就绪状态的线程，再次使用start（）
- Running 运行
    - 当处于就绪状态时，该线程获得cpu，执行体开始运行，就处于运行状态了
- Blocked 阻塞
    - 线程不可能一直处于运行状态（线程执行体足够短，瞬间就可以完成的线程排除），线程会在运行过程中需要被中断，因为是并发，目的是会让其他线程获得执行的机会，线程的调度细节取决于OS采用的策略。
    - （抢占式调度xp win7 linux - unix..）。
    - 如果是一些特殊的小型设备可能采用协作式调度（只有线程自己调用它的sleep（）或yield（）才会放弃所占用的资源）。
- Dead死亡
    - 测试某条线程是否已经死亡，可以调用线程对象的isAlive（）方法，当线程处于就绪，运行，阻塞时，返回true。线程处于新建，死亡时返回false。
    - 不能对已经死亡的线程调用start（）方法使它重新启动，死亡就是死亡，是不能再次作为线程执行的。
- 当主线程结束时候，其他线程不受任何影响，并不会随之结束。一旦子线程启动起来后，它就拥有和主线程相同的地位，它不会受到主线程的影响。

### 线程控制
#### JOIN
- 等待该线程结束后再往下执行
- 方法
    - Join（）
        - 等待被join的线程执行完成
    - Join（long millis）
        - 等待join线程的时间最长为millis毫秒，如果在这个时间内，被join的线程还没有执行结束则不再等待）
    - Join（long millis，int nanos）
        - 千分之一毫秒（不用）
- 实例
    ```
    JoinThread joinThread= new JoinThread();
    new Thread(joinThread).start();
    for(int i=0; i< 4; i++) {
        Thread thread = new Thread(joinThread);
        thread.start();
        thread.join();      //若只针对最后一个调用，则最后一个调用完成，可能其它线程还未完成也会继续往下执行而导致数据未完全
    }
    for(int i=0; i< 3; i++){
        System.out.println(Thread.currentThread().getName() + "\t" + i);
    }
    ```
- 返回结果
    ```
        Thread-0    0
        Thread-1    0
        Thread-0    1
        Thread-1    1
        Thread-1    2
        Thread-0    2
        Thread-2    0
        Thread-2    1
        Thread-2    2
        Thread-3    0
        Thread-3    1
        Thread-3    2
    main    0
    main    1
    main    2
    ```

#### 后台进程
- 示例
    ```
    JoinThread joinThread= new JoinThread();
    Thread thread= new Thread(joinThread);
    thread.setDaemon(true);     //必须于执行前设置。前台线程结束后，会再执行一段时间后台线程。
    thread.start();
    for(int i=0; i< 10; i++){
        System.out.println(Thread.currentThread().getName() + "\t" + i);
    }
    ```

- 返回结果
    ```
    main    0
    main    1
    main    2
    main    3
        Thread-0    0
    main    4
        Thread-0    1
    main    5
        Thread-0    2
    main    6
        Thread-0    3
    main    7
        Thread-0    4
    main    8
        Thread-0    5
    main    9
        Thread-0    6
        Thread-0    7
        Thread-0    8
        Thread-0    9
        Thread-0    10
        Thread-0    11
        Thread-0    12
    ```

#### SLEEP
- 休眠，状态转阻塞

#### YIELD
- 线程让步，状态转就绪，让步于优先级相同或更高的线程


### 多线程同步
#### 同步代码块
```
Synchronized(obj){ 
    //...同步代码块 
} 
```

#### 同步方法
```
public synchronized void draw(){  ...}
```

#### 锁定释放时机
- 调用执行结束
- break或return
- 代码中出现Error或Exception
- 代码中执行监视器对象的wait进行当前线程的暂停释放

#### 不释放情况
- 调用Thread.sleep/yield
- 其它线程调用该线程的suspend将之挂起（不推荐使用）

#### 同步锁LOCK
```
private final ReentrantLock relock=new ReentrantLock();  //声明锁对象
public void run(){
    relock.lock();      //加锁 
    try{   
        ... //同步执行代码
    }finally{  //释放锁 
        relock.unlock(); 
    }
}
```

#### 死锁
- 当两个线程相互等待对方释放同步监视器的时候就会发生死锁，一旦出现死锁，整个程序既不会发生任何异常，也不会有任何提示，只是所有线程处于阻塞状态，无法继续。


### 线程通信
- 线程协调运行
    - Object: wait()| notify()|| notifyAll()
- 使用条件变量来控制协调
    - Condition
        - `await()|| signal()|| signalAll()`
            - `private final Lock lock=new ReentrantLock(); `
            - `private final Condition cond=lock.newCondition(); `
- 管道流通信
    - 创建管道输入输出流
        - `PipedWriter pw = new PipedWriter();`
        - `PipedReader pr = new PipedReader();`
    - 管道连接
        - `pr.connect(pw);`
    - 将管理分别传入处理线程并调用实现
        - `new Thread(new ReaderThread(pr),"读取管道线程").start();`
            - `pw.write(str)`
        - `new Thread(new WriterThread(pw),"写入管道线程").start();`
            - `while ((buffer = br.readLine()) != null)`

- 线程组
    - 介绍
        - 对线程组的控制相当于同时控制这批线程。
        - 用户创建默认属于默认线程组，如子线程和创建它的主线程同处同一线程组。
        - 一旦加入指定线程组，中途不允许调整。
        - `Thread th=new Thread(new ThreadGroup("私人"), new GroupThread(), "线程1");`




## 反射
- 机制
    - 在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制
- 用途
    - 在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；
    - 对于任意一个对象，都能够调用它的任意一个方法；
- 实例
    - 主要针对框架开发，如jsp的javabean,spring的自动注入，hibernate及struts
- 代码
    ```
    public class ReflectionTest {
        //测试变更属性，其中为了直观调用属性属性设置为public类型
        public double field;

        public static void main(String[] args) {
            List2Obj();
        }

        /***
         * 构造函数（无参-默认& 带参）
         */
        public ReflectionTest() {}
        public ReflectionTest(Double field) {
            this.field = field;
        }

        /**
         *  动态获取数据对象，可将多维数组的部分结构拼成对象来调用
         */
        public static void List2Obj(){
            int dims[] = new int[]{5, 10, 15};
            Object arr = Array.newInstance(Integer.TYPE, dims);
            Object arrobj = Array.get(arr, 3);
            Class cls = arrobj.getClass().getComponentType();
            System.out.println(cls.getSimpleName());
            arrobj = Array.get(arrobj, 5);
            Array.setInt(arrobj, 10, 37);
            int arrcast[][][] = (int[][][]) arr;
            System.out.println(arrcast[3][5][10]);
        }

        /***
         * 调用参数的方法进行属性更新
         */
        public static void callFieldChange(){
            Class c = null;
            try {
                c = Class.forName("com.yao.controller.reflection.ReflectionTest");
                Field field= c.getField("field");
                ReflectionTest reflect= new ReflectionTest(12.34D);
                System.out.println(reflect.field);
                field.setDouble(reflect, 43.21);
                System.out.println(reflect.field);
            }catch (Exception e){
                e.printStackTrace();
            }
        }

        /***
         * 专门用于提供反射调用
         * @param a 加数1
         * @param b 加数2
         * @return  a+b的结果
         */
        public int add(int a, int b){
            return a+ b;
        }

        /***
         * 调用本方法内的ADD方法
         *      Constructor方式与之相似
         */
        public static void callClassMethod(){
            Class c = null;
            try {
                c = Class.forName("com.yao.controller.reflection.ReflectionTest");

                //指定方法和参数类型以获取类中对应的方法
                Class types[]= new Class[2];
                types[0]= Integer.TYPE;
                types[1]= Integer.TYPE;
                Method method= c.getMethod("add", types);

                //针对各参数位填充以实际数值
                Object args[]= new Object[2];
                args[0]= new Integer(1);
                args[1]= new Integer(2);
                Integer retVal= (Integer)method.invoke(c.newInstance(), args);
                System.out.print(retVal);

            }catch (Exception e){
                e.printStackTrace();
            }
        }

        /***
         * 输出String类大致文档结构
         */
        public static void printClssInfo(){
            Class c = null;
            try {
                c = Class.forName("java.lang.String");
                System.out.println("package " + c.getPackage().getName() + ";");
                System.out.print(Modifier.toString(c.getModifiers()) + " ");
                System.out.print("class " + c.getSimpleName() + " ");
                if (c.getSuperclass() != Object.class) {
                    System.out.print("extends " + c.getSuperclass().getSimpleName());
                }
                Class[] inters = c.getInterfaces();
                if (inters.length > 0) {
                    System.out.print("implements ");
                    for (int i = 0; i < inters.length; i++) {
                        System.out.print(inters[i].getSimpleName());
                        if (i < inters.length - 1) {
                            System.out.print(",");
                        }
                    }
                }
                System.out.println("{");
                printFields(c);
                printMethods(c);
                System.out.println("}");
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            }
        }

        /***
         * 打印类的所有参数
         * @param c
         */
        public static void printFields(Class c) {
            Field[] field = c.getDeclaredFields();
            if (field.length > 0) {
                for (int i = 0; i < field.length; i++) {
                    System.out.println(Modifier.toString(field[i].getModifiers()) + " " + field[i].getType().getSimpleName() + " " + field[i].getName() + ";");
                }
            }
        }

        /***
         * 打印类的所有方法
         * @param c
         */
        public static void printMethods(Class c) {
            Method[] method = c.getDeclaredMethods();
            if (method.length > 0) {
                for (int i = 0; i < method.length; i++) {
                    Class[] parameter = method[i].getParameterTypes();
                    System.out.print(Modifier.toString(method[i].getModifiers()) + " " + method[i].getReturnType().getSimpleName() + " " + method[i].getName() + "(");
                    for (int j = 0; j < parameter.length; j++) {
                        System.out.print(parameter[j].getSimpleName() + " args");
                        if (j != parameter.length - 1) {
                            System.out.print(",");
                        }
                    }
                    System.out.print(") ");
                    Class exception[] = method[i].getExceptionTypes();

                    if (exception.length > 0) {
                        System.out.print("throws ");
                        for (int j = 0; j < exception.length; j++) {
                            System.out.print(exception[j].getSimpleName());
                        }
                    }
                    System.out.println("{");
                    System.out.println("\t... ...");
                    System.out.println("}");
                }

            }
        }
    }
    ```

## 注解
### 自定义注解
- 代码格式
    ```
    @Target({ElementType.METHOD, ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    public @interface LoginRequired {
    }
    ```

- 框架详解
    - Target
        - 指定该注解使用的位置，可选情况如下
        ```
        public enum ElementType {   
            TYPE,                 // 指定适用点为 class, interface, enum   
            FIELD,                 // 指定适用点为 field   
            METHOD,             // 指定适用点为 method   
            PARAMETER,             // 指定适用点为 method 的 parameter   
            CONSTRUCTOR,         // 指定适用点为 constructor   
            LOCAL_VARIABLE,     // 指定使用点为 局部变量   
            ANNOTATION_TYPE,     //指定适用点为 annotation 类型   
            PACKAGE             // 指定适用点为 package   
        }
        ```
    - @Retention
        - 指定编译器处理的方式，可选情况如下
            ```
            public enum RetentionPolicy { 
                SOURCE,     // 编译器处理完Annotation后不存储在class中，仅存在于源文件中
                CLASS,         // 编译器把Annotation存储在class中，但不能被VM读取，这是默认值 
                RUNTIME     // 编译器把Annotation存储在class中，可以由虚拟机读取,反射需要 
            }
            ```
    - @Documented     
        - 指定允许写入javadoc
    - @Inherited
        - 允许子类继承时同时继承该注解
            ```
            public @interface LoginRequired {
                String value() default "login";   
            }
            ```
    - @Constraint(指定用哪个类进行相关校验)
        - @Constraint(validatedBy = {SafeStringValidator.class, SafeStringListValidator.class})    //注解类 
        - public class SafeStringValidator implements ConstraintValidator<SafeString, String> {    //校验实现类

- JAVA内置
    - Override只用于方法,它指明注释的方法重写父类的方法,如果不是,则编译器报错.
    - Deprecated指明该方法不建议使用
    - SuppressWarnings告诉编译器:我知道我的代码没问题





## 执行jar包
- 需求
    - 提供DES加密包，支持命令行调用输出
- 解决方式
    - 独立的Maven项目实现，并创建Main方法进行密码和密钥的输入，本地固定值测试OK后调整取args输入
    - 将该项目install为jar包，并使用WinRAR工具在MANIFEST.MF中添加main方法入口（Main-Class: test.someClassName - 含main方法的类的项目内完整路径及名称）
    - 控制台输入 java -jar c:/Encrypt.jar "password" "secret key"得到相应的加密后结果
























