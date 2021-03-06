
## 读代码
### 启动
Bootstrap 
main
    init()
        createClassLoader
        catalinaDaemon = new Catalina() // 反射
    setAwait()
        catalinaDaemon.setAwait()
    load() // start a new server instance
        catalinaDaemon.load()
            digester = createStartDigester() // 解析 conf/server.xml
                        Server:org.apache.catalina.core.StandardServer
                        Server/GlobalNamingResources:org.apache.catalina.deploy.NamingResourcesImpl
                        Server/Service:org.apache.catalina.core.StandardService
                        Server/Service/Executor:org.apache.catalina.core.StandardThreadExecutor'
                        Server/Service/Connector:server.xml
            digester.parse(inputstream) // JAXPSAXParser.parse(inputstream)，之后 catalina 包含了 server.xml 中的所有对象
            StandardServer.init(); // Catalina 从 conf/server.xml 中解析得到
                StandardServer.initInternal()
                    services[i].init()
                        StandardService.initInternal()
                            Container.init() 
                                -> StandardEngine.initInternal() -> ContainerBase.initInternal() -> LifecycleMBeanBase.initInternal()
                            Executor.init()
                                StandardThreadExecutor.initInternal()
                                (通过 TaskQueue 重写 offer 方法从而改变了线程池得行为:先核心,再到达最大线程，最后如队列)
                            MapperListener.init()

                            Connector.initInternal()
                                -> protocolHandler = org.apache.coyote.http11.Http11NioProtocol
                                -> org.apache.coyote.http11.Http11Protocol
                                -> protocolHandler = org.apache.coyote.ajp.AjpNioProtocol

                                -> adapter = CoyoteAdapter
                                    -> Http11Protocol.init()
                                        JIoEndpoint.init() // one listener thread accepts on a socket 
                                                           // and creates a new worker thread for each incoming connection
                                            serverSocket = serverSocketFactory.createSocket(getPort(), getBacklog(), getAddress()) 
                                                           // new ServerSocket (port, backlog, ifAddress)
                                            handler: Http11ConnectionHandler
                                    -> Http11NioProtocol.init()
                                        NioEndpoint.init()
                                            serverSock = ServerSocketChannel.open()
                                            serverSock.socket().bind()
                                            NioSelectorPool.open()// Thread safe non blocking selector pool 看不懂了
                                                SHARED_SELECTOR = Selector.open() // 单例
                                                NioBlockingSelector
                                                    sharedSelector
                                                    poller = new BlockPoller()
                                            stopLatch = availableProcessors
                state: LifecycleState.NEW -> LifecycleState.INITIALIZED
    start()
        catalinaDaemon.start()
            StandardServer.startInternal()
                state: LifecycleState.STARTING
                services[i].start()
                    StandardService.startInternal()
                        StandardEngine.startInternal() -> ContainerBase.initInternal()
                    Executor.start()
                        StandardThreadExecutor.start()//tomcat-exec-
                            taskqueue = new TaskQueue(maxQueueSize=Integer.MAX_VALUE) // 可以设置
                            executor = new ThreadPoolExecutor(25, 200, 60000, TimeUnit.MILLISECONDS, taskqueue, tf);
                            默认false:executor.prestartAllCoreThreads();
                    MapperListener.start()
                    Connector.start()
                            -> org.apache.coyote.http11.Http11NioProtocol.start()
                            -> org.apache.coyote.http11.Http11Protocol.start()
                                JIoEndpoint.start()
                                     -> executor =  new ThreadPoolExecutor(10, 200, 60, TimeUnit.SECONDS,taskqueue, tf)
                                     -> acceptorThreadCount 个 // Start acceptor threads 
                                            Acceptor // Server socket acceptor thread
                                                -> getExecutor().execute(new SocketProcessor(new SocketWrapper<Socket>(socket)));
                                                    -> Http11ConnectionHandler.process(socket, SocketStatus.OPEN); // inner Http11Protocol  
                                                        Http11Protocol
                                                        Http11Processor = createProcessor() // Processes HTTP requests.
                                                                -> Request(-> inputBuffer)
                                                                -> Response
                                                            -> processor.process(socket)
                                                                    -> inputBuffer.setInputStream(socket.getSocket().getInputStream());
                                                                    -> outputBuffer.setOutputStream(socket.getSocket().getOutputStream());
                                                                    -> inputBuffer.parseRequestLine(false)
                                                                    -> inputBuffer.parseHeaders()
                                                                    -> prepareRequest
                                                                    
                            -> org.apache.coyote.ajp.AjpNioProtocol.start()
                                NioEndpoint.start() //
                                    executor =  new ThreadPoolExecutor(10, 200, 60, TimeUnit.SECONDS,taskqueue, tf)
                                    poller threads -> Poller
                                    acceptor threads


## 设计模式
工厂，
Chain of Responsibility
event/listener


## 服务接入层
反向代理 Nginx, API 网关 Node.js
## 业务逻辑层
Web 容器 Tomcat，Jetty; 应用层框架 Spring,Spring MVC 和 Spring Boot; ORM 框架 MyBatis.
## 数据缓存层
内存数据库 Redis, 消息中间件 Kafka
## 数据缓存层
关系型数据库 MySQL, 非关系型数据库 MongoDB; 文件存储 HDFS; 搜索分析引擎 Elasticsearch

微服务之间需要相互调用, 于是出现了 RPC 框架: Spring Cloud 和 Dubbo.
网络通信: Netty
分布式协调: Zookeeper 

通用技术：网络编程，多线程，反射，类加载 这在不少中间件或框架中都会用到。

比如 
网络通信
浏览器将 Http 请求发给 Web 容器，一个微服务要调用另一个微服务，WEB 应用读写缓存服务器，消息队列或数据库
考虑问题
1. I/O 模型是同步还是异步，是阻塞还是非阻塞
2. 通信协议是二进制(gRPC)还是文本(HTTP)
3. 数据怎么序列化， JSON 还是 Protocol Buffer

服务器端线程模型也是一个重点。
一般来说服务器端有几个角色
1. Acceptor 负责接收新链接: accept
2. Selector 负责检测连接上的 I/O 事件: select
3. Processor 负责数据读写，编解码和业务处理: read decode process encode send


I/O
计算机内存与外部设备之间拷贝数据的过程
CPU 发出读指令，数据从外部设备拷贝到内存需要一段时间，这个时候CPU怎么知道数据到了

同步阻塞IO: 用户发起 read 调用后阻塞，让出 CPU。内核等待网卡数据到来，把数据从网卡拷贝到内核空间，接着把数据拷贝到用户空间。再把线程叫醒。
同步非阻塞IO: 用户线程不断的发起 read 调用，数据每到内核空间时，每次都返回失败(没有阻塞)，知道数据到了内核空间。这一次read调用后，在等待数据从内核空间拷贝到用户空间这段时间，线程还是阻塞的。等数据到了用户空间再把线程叫醒

IO多路复用：用户线程读取操作分成了两步，线程先发起 select 目的是问内核数据准备好了么，准备好了用户线程再发起 read 调用。等待数据从内核空间拷贝到用户空间这段时间里，线程还是阻塞。因为一次 select 调用可以向内核查多个数据通道(Channel)状态，所以叫多路复用

异步IO: 发起 read 的同时注册一个回调函数，read 立即返回，等内核将数据准备好后，再调用指定的回调函数处理完成。在这个过程中用户线程没有阻塞


NioEndpoint I/O 多路复用










