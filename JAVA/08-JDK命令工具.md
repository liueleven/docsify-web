# JDK命令工具

> 写于2018-04-26

> 更新2018-07-18

- jps
> 列出所有java进程

- jstack
> 输出线程的dump文件

- javap 
> -v -c  .class文件,反编译字节码

- jps 进程状况
  > jps -l 查看正在运行的进程

  > jps -v 虚拟机进程启动时的JVM参数

- jstat 统计信息

- jinfo java配置信息工具
- jmap  java内存映像
- jstack 
- jar 
    > jar -xvf *.jar解压jar包
     
    > jar -cvfM0 xxx.jar * 当前所有文件打成jar包 
     
    > jar -xvf *.war 解压war包 
    
    > jar -cvfM0 hello.war ./ 当前所有文件打成war包
