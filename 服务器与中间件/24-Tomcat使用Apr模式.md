# Tomcat8开启Apr模式
> 写于：2018-09-15

### 三种模式
Tomcat8默认是开启Apr模式，但是必须有相关库，如果没有就开启NIO模式，这里的模式指的是对请求的处理模式，有三种：
- BIO
    > 每来一个请求创建一个线程来处理,同步并阻塞
- NIO
    > 每来一个请求创建一个线程来处理，但它可以复用，也就是同步不阻塞
- APR
    > 每来一个请求创建一个线程来处理，但它是系统级别处理方式，异步非阻塞

### 环境配置
Tomcat 8.5.33

Jdk 8

Centos 7

### Apr配置步骤
- 先安装相关依赖库
    ```
    yum install apr-devel openssl-devel
    ```
- 安装编译native源码包
    ```
    进入tomcat/bin目录: cd $tomcat$/bin
    解压tomcat-native-tar.gz: tar -zxvf tomcat-native-tar.gz
    进入native目录：cd $tomcat$/bin/tomcat-native-1.2.17-src/native
    创建apr目录：mkdir -p /usr/bin/apr-config
    配置指定依赖项和jdk目录：
    ./configure --with-apr=/usr/bin/apr-config --with-java-home="/usr/local/jdk" --with-ssl=yes
    创建lib存储目录：mkdir -p /usr/java/packages/lib/amd64
    进入该目录：cd /usr/java/packages/lib/amd64
    将apr的lib目录在这里创建软链接： 
    ln -s  /usr/local/apr/lib/libtcnative-1.so.0.2.17 libtcnative-1.so
    ln -s  /usr/local/apr/lib/libtcnative-1.so.0.2.17 libtcnative-1.so.0
    ```
- 启动tomcat
    ```
    sh $tomcat$/bin/start.sh
    ```
- 查看tomcat日志验证配置结果
    ```
    vi $tomcat$/logs/catalina.out
    ```
- 在日志的前面几行注意查看日志
![image](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/12-tomcat8Apr%E6%A8%A1%E5%BC%8F%E9%85%8D%E7%BD%AE/01-Apr%E9%85%8D%E7%BD%AE%E6%88%90%E5%8A%9F%E9%AA%8C%E8%AF%81.png)

