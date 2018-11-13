# 初识Tomcat
> 写于2018-04-28

> 更新2018-09-16

### 目录结构

- conf **Tomcat相关配置文件**
    - catalina.polity:    Tomcat安全策略文件，控制jvm相关权限
    - catalina.perproties: catalina行为控制配置文件
    - logging.properties：Tomcat日志文件
    - server.xml:  Tomcat server配置文件
    - tomcat-user：角色配置文件
    - context.xml：全局context配置
    - web.xml：Servlet标准的配置文件，Tomcat默认实现了部分配置
    
- lib **存放Tomcat会用到的jar包**
    - ecj-4.4.2.jar：Eclipse java编译器
    - jasper.jar：jsp编译器
    
- logs **Tomcat相关日志**
    - localhost.yyyy-MM-dd.log：Tomcat异常日志
    - catalina.yyyy-MM-dd.log：相当于控制台的输出
    
#### web部署

- 放置在webapps目录下   
    - 直接把war包放到该目录下，启动Tomcat，会自动解压war包，然后再浏览器访问 **ip:port/war包名称** 端口默认是8080 

- 修改配置文件conf/server.xml 
     - 添加Context标签，docBase：映射的路径，path：虚拟路径，<Context docBase="${war包路径}" path="/tset/可以加前缀" reloadable="true" />
     


#### 乱码问题？？？？？？
- 在conf/server.xml文件中的Connector标签加上URIEncoding="UTF-8"然后再代码上写上响应contentType

```
//在conf/server.xml配置问加你中添加
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8"/>
               
//在代码中设置contentType               
resp.setContentType("text/html;charset=utf-8");
```

### Tomcat8安全管理与性能优化
- 删除weabapps目录下的所有文件
- server.xml中有一行代码：`<Server port="8005" shutdown="SHUTDOWN">`也就是说只要能够telnet ip:8005端口就可以关掉服务器了，所以要把`SHUTDOWN`改成其他任意字符，或者把8005改成-1
- 服务器防火墙只开启特定的端口
- 修改conf/server.xml文件中线程池
    ```
    修改Executor
    修改前
     <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
    maxThreads="150" minSpareThreads="4"/>
    修改后
    <Executor
     name="tomcatThreadPool"
     namePrefix="catalina-exec-"
     maxThreads="500"
     minSpareThreads="30"
     maxIdleTime="60000"
     prestartminSpareThreads = "true"
     maxQueueSize = "100"
    />
    
    修改Connector
    修改前
    <Connector 
     port="8080" 
     protocol="HTTP/1.1" 
     connectionTimeout="20000" 
     redirectPort="8443" 
     />
     修改后
     <Connector
     executor="tomcatThreadPool"
     port="改成你要的端口"
      URIEncoding=”UTF-8”
     protocol="org.apache.coyote.http11.Http11Nio2Protocol"
     connectionTimeout="60000"
     maxConnections="10000"
     redirectPort="8443"
     URIEncoding="utf-8"
     server="Server Version 11.0"
     compression="on" compressionMinSize="2048"
     compressableMimeType="text/html,text/xml,text/javascript,text/css,text/plain"
     redirectPort="8443" />
     # 注释：
      URIEncoding=”UTF-8” 可以对url的中文进行转码
      maxSpareThreads 如果空闲状态的线程数多于设置的数目，则将这些线程中止，减少这个池中的线程总数
      minSpareThreads 最小备用线程数，tomcat启动时的初始化的线程数
      enableLookups 这个功效和Apache中的HostnameLookups一样，设为关闭
      acceptCount 当线程数达到maxThreads后，后续请求会被放入一个等待队列，如果队列满了，则拒绝连接
      compression="on" 打开压缩功能
      compressionMinSize="2048" 启用压缩的输出内容大小，这里面默认为2KB
      noCompressionUserAgents="gozilla, traviata"
      compressableMimeType="text/html,text/xml"　压缩类型
      对于以下的浏览器，不启用压缩
    ```
- tomcat 使用apr模式来处理请求，需要安装一些依赖库，操作步骤如下：
```
看另外一篇教程
```

- tomcat启动命令参数调优
    
    在tomcat启动catalina.sh加入配置参数，jvm会将这些参数加载进去，如何验证？可以在加入前利用jdk工具看下配置，加入后看下配置在$tomcat$/bin中的catalina.sh文件中搜索if [ $have_tty -eq 1 ]; then在这句下面加上参数配置
    
    
    CATALINA_OPTS="
    -server
    -Xms6000M
    -Xmx6000M
    -Xss512k
    -XX:NewSize=2250M
    -XX:MaxNewSize=2250M
    -XX:PermSize=128M
    -XX:MaxPermSize=256M
    -XX:+AggressiveOpts
    -XX:+UseBiasedLocking
    -XX:+DisableExplicitGC
    -XX:+UseParNewGC
    -XX:+UseConcMarkSweepGC
    -XX:MaxTenuringThreshold=31
    -XX:+CMSParallelRemarkEnabled
    -XX:+UseCMSCompactAtFullCollection
    -XX:LargePageSizeInBytes=128m
    -XX:+UseFastAccessorMethods
    -XX:+UseCMSInitiatingOccupancyOnly
    -Duser.timezone=Asia/Shanghai
    -Djava.awt.headless=true"
        
    参数解释：
    -server  默认，必须加
    -Xms jvm堆内存最小值     备注：要根据机器性能来设置，比如在命令行中输入：java -Xmx2048m -version 没有错误信息，表示可以，具体还得百度
    -Xmx jvm堆内存最大值     备注：堆=新生代+老年代+持久代，持久代一般为64m，新生代占总的3/8,其余都都给老年代。【老年代要占这么多？具体还得百度】
    -Xmn 新生代的大小
    -Xss 设定每个线程的堆栈大小，一般不易设置超过1M，要不然容易出现out ofmemory
    -XX:+UseBiasedLocking 线程锁的优化
    # PermSize永久代JDK8不用它进行控制了，直接放在物理内存硬盘上
    -XX:PermSize=128M 非堆区初始化内存，永生代是不会被回收的需要注意，默认是物理内存的1/64
    -XX:MaxPermSize=256M 非堆区的最大值，默认是物理内存的1/4
    -XX:+DisableExplicitGC 禁止在代码中使用显示GC，System.gc();这个会让应用出现暂停现象
    -XX:+UseParNewGC 对年轻代使用多线程进行回收
    -XX:+UseConcMarkSweepGC 使用CMS进行GC
    -XX:+CMSParallelRemarkEnabled 在使用UseParNewGC 的情况下, 尽量减少
    -XX:+UseCMSCompactAtFullCollection
    在使用concurrent gc 的情况下, 防止 memoryfragmention, 对live object 进行整理, 使 memory 碎片减少mark 的时间
    -XX:LargePageSizeInBytes 指定 Java heap的分页页面大小
    -XX:+UseFastAccessorMethods get,set 方法转成本地代码
    -XX:+UseCMSInitiatingOccupancyOnly 指示只有在 oldgeneration 在使用了初始化的比例后concurrent collector 启动收集
    -Djava.awt.headless=true 图表工具的支持
    
