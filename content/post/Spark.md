---
title: "Hello.Spark"
date: "2017-10-20"
categories:
 - "阅读"
tags:
 - "框架"
 - "Spark"
toc: true
---


# Spark

## 启动
- Spark
    - method
        - setUp
            - threadPool
            - ipAddress
            - port
            - secure
        - start
            - init
            - stop
            - webSocket
        - other
            - <method>(String path, Route route)
                - get
                - post
                - put
                - patch
                - delete
                - head
                - trace
                - connect
                - options
                - before
                - after
            - <method>(String path, String acceptType, Route route)
            - webSocket(String path, Class<?> handler)
    - class
        - SingletonHolder
            - `static final SparkInstance INSTANCE = new SparkInstance();`
        - SparkInstance
            - parameter
                - port
                - ipAddress
                - max|min Threads
                - webSocketHandlers
                - SparkServer
                - SimpleRouteMatcher
                - CountDownLatch
            - method
                - init()
                    - `this.routeMatcher = RouteMatcherFactory.get();`
                    - `(new Thread(run<invokedynamic>(this))).start();`
                - getter/ setter
                - webSocket(String path, Class<?> handler)
                - addRoute(String httpMethod, RouteImpl route)
                - addFilter(String httpMethod, FilterImpl filter)
            - extends
                - Runnable
            - used
                - HaltException
                - **JettySparkServer**
                    - parameter
                        - Handler
                        - Server
                    - method
                        - ignite(String host, int port, SslStores sslStores, String staticFilesFolder, String externalFilesFolder, CountDownLatch latch, int maxThreads, int minThreads, int threadIdleTimeoutMillis, Map<String, Class<?>> webSocketHandlers, Optional<Integer> webSocketIdleTimeoutMillis)
                    - impl
                        - SparkServer
                            - ignite
                                - init
                                    - `Ignites the spark server, listening on the specified port, running SSL secured with the specified keystore and truststore`
                                    - `JettyServerFactory.createServer(maxThreads, minThreads, threadIdleTimeoutMillis);`
                                    - `SocketConnectorFactory.createSocketConnector(server, host, port);`
                                    - `WebSocketServletContextHandlerFactory.create(webSocketHandlers, webSocketIdleTimeoutMillis);`
                                    - `server.setHandler(handlers);`
                                - start
                                    - `server.start();`
                                    - `latch.countDown();`
                                        - `CountDownLatch latch = new CountDownLatch(1);`
                                    - `server.join();`
                            - stop
                                 - `server.stop();`
                    - uesd
                        - Handler
                            - impl
                                - AbstractHandler
                                    - impl
                                        - impl
                                            - HandlerWrapper
                                                - method
                                                    - void handle(String target, Request baseRequest, HttpServletRequest request, HttpServletResponse response)
                                                        - super.handle(target, baseRequest, request, response);
                                                        - cehck resource exists/ endsWith
                                                            - `response.setContentType("text/css");`
                                                        - check last_modified/ etag/ mime
                                                            - set http header
                                                            - doResponseHeaders(HttpServletResponse response, Resource resource, String mimeType)
                                                            - doDirectory(HttpServletRequest request,HttpServletResponse response, Resource resource)
                        - Server
                            - parameter
                                - SessionIdManager
                                - List<Connector>
                                - ThreadPool
                                - AttributesMap
                                - RequestLog
                            - method
                                - doStart()
                                    - `ShutdownMonitor.getInstance().start();`
                                 - doStop()
                                    - List<Future<Void>> futures
                                        - `futures.add(((Graceful)graceful).shutdown());`
                                            - 添加各连接、子处理器的关闭进程结果
                                        - `future.isDone()`
                                            - 检查是否均已关闭成功
                                        - `connector.stop();`
                                - handle(HttpChannel connection)
                                    - !HttpMethod.OPTIONS.is(request.getMethod())
                                        - `response.sendError(HttpStatus.BAD_REQUEST_400);`
                                    - `handler.handle(target,baseRequest, request, response);`
                            - used
                                - HttpField
                                    - parameter
                                        - HttpHeader
                                        - name
                                        - value
                                        - hash
                                    - method
                                        - getter/ setter
                                    - used
                                        - HttpHeader
                                            - enum
                                            - 请求头各项
                                - ShutdownMonitor
                                    - parameter
                                        - host
                                        - port
                                        - key
                                            - stop.key
                                        - ServerSocket
                                        - Thread
                                    - method
                                        - start()
                                            - new Thread(new ShutdownMonitorRunnable()).start()
                                         - stopLifeCycles (boolean destroy)
                                        - void stopOutput (Socket socket)
                                        - void stopInput (Socket socket)
                                    - used
                                        - ShutdownMonitorRunnable
                                            - method
                                                - ShutdownMonitorRunnable()
                                                    - startListenSocket()
                                                - run()
                                                    - serverSocket.accept().getInputStream()
                                                    - "stop".equalsIgnoreCase(cmd)
                                                        - _lifeCycles.stop()
                                                        - _lifeCycles.destroy()
                                                - stopInput (Socket socket)
                                                    - close(serverSocket);
                                                - stopOutput (Socket socket)
                                                    - socket.shutdownOutput();
                                                    - close(socket);
                                                - startListenSocket()
                                                    - `serverSocket = new ServerSocket();`
                                                    - `serverSocket.bind(new InetSocketAddress(InetAddress.getByName(host), port), 1);`
                                                        - InetAddress.getByName(host)
                                                            - 解析地址
                                                    - `key = Long.toString((long)(Long.MAX_VALUE * Math.random() + this.hashCode() + System.currentTimeMillis()),36);`
                                        - LifeCycle
                                            - method
                                                - server
                                                    - start()
                                                    - stop()
                                                - status
                                                    - isRunning()
                                                    - isStarted()
                                                    - isStarting()
                                                    - isStopping()
                                                    - isStopped()
                                                    - isFailed()
                                        - ServerSocket
                                            - parameter
                                                - bCreated
                                                - bBound
                                                - bClosed
                                                - bOldImpl
                                                - closeLock
                                                - SocketImpl
                                        - SocketImpl
                                            - impl
                                                - AbstractPlainSocketImpl
                                                    - method
                                                        - create
                                                        - connect
                                                        - bind
                                                        - listen
                                                        - accept
                                                    - impl
                                                        - TwoStacksPlainSocketImpl
                                                            - method
                                                                - native void socketCreate(boolean isServer)
                                                                    - native 关键字表明该方法由其它语言实现进行操作系统的操作
                                                        - DualStackPlainSocketImpl
                                                        - PlainSocketImpl
                                                            - impl
                                                                - HttpConnectSocketImpl
                                                                - SocksSocketImpl
                                                                - SdpSocketImpl
                            - extends
                                - HandlerWrapper
                - SimpleRouteMatcher
                    - parameter
                        - List<RouteEntry> routes
                    - method
                        - addRoute(HttpMethod method, String url, String acceptedType, Object target)
                            - List<RouteEntry>
                        - removeRoute(String path, String httpMethod)
                        - findTargetsForRequestedRoute(HttpMethod httpMethod, String path)
                - RouteEntry
                    - parameter
                        - httpMethod
                        - path
                        - acceptedType
                        - target
                    - method
                        - boolean matchPath(String path)
                        - boolean matches(HttpMethod httpMethod, String path)
                    - used
                        - HttpMethod
                            - Enum
                                - get
                                - post
                                - put
                                - patch
                                - delete
                                - head
                                - trace
                                - connect
                                - options
                                - before
                                - after
                        - parameter
                            - target
                            - matchUri
                            - requestURI
                            - acceptType
- 理解
    - 服务启动
    - 端口监听
    - handler 构造


## 渲染
- Configurable
    - parameter
        - setUp
            - locale
            - *Format
            - timeZone
            - encode
            - autoFlush
            - variables
        - utils
            - objectMapper
            - templateExceptionHandler
            - TemplateClassResolver
    - extends
        - Template
            - parameter
                - macros
                - imports
                - TemplateElement
                - lines
                - name/ encoding/ defaultNS - namespace
            - method
                - process(Object rootMap, Writer out, ObjectWrapper wrapper, TemplateNodeModel rootNode)
                    - createProcessingEnvironment(rootMap, out, wrapper)
                - Environment createProcessingEnvironment(Object rootMap, Writer out, ObjectWrapper wrapper)
                    - convert map to Enviroment
            - class
                - LineTableBuilder
                    - 处理行信息中回车、TAB
            - Used
                - FMParser
                - TemplateElement
                    - 树形结构
                - TemplateCache
                    - parameter
                        - delay
                        - CacheStorage
                        - localizedLookup
                        - Configuration
                    - method
                        - removeTemplate(String name, Locale locale, String encoding, boolean parse)
                            - storage.remove(tk);
                        - getTemplate(String name, Locale locale, String encoding, boolean parse)
                            - storage.get(tk)
                    - class
                        - CachedTemplate
                            - parameter
                                - templateOrException
                                - source
                                - lastChecked
                                - lastModified
                        - TemplateKey
                            - parameter
                                - name
                                - locale
                                - encoding
                                - parse
                - CacheStorage
                    - method
                        - get
                        - put
                        - remove
                        - clear
- TemplateEngine
    - parameter
        - Configuration
    - method
        - String render(ModelAndView modelAndView)
            - this.configuration.getTemplate(modelAndView.getViewName());
            - template.process(modelAndView.getModel(), e);
    - extends
        - FreeMarkerEngine
- 理解
    - 初始化解析器
        - 资源加载
        - 格式转换
    - 模板存取
    - 模板的应用、语法解析
    - 国际化、语种等相关通用设置
