# Linux常用命令

> 写于2018-04-26

> 更新2018-07-18

#### 读完本篇，你能获得啥？
linux常用的操作命令 

**多多使用 -help 查看命令帮助！！！**

#### 快捷键
- ctrl + a  回到行首
- ctrl + e  回到行尾
- ctrl + k  删除光标到行尾的字符
- ctrk + u  删除光标前的字符
- ctrl + l  清屏
- vi中按esc dd    删除一行

#### 查系统配置数据
- ip addr
    > 查看ip地址
- cat /proc/version
    > 查看linux系统版本信息
- cat /proc/cpuinfo
    > 查看cpu信息
- free
    > 查看内存大小
    
#### 文件操作
- more 文件名 直接在屏幕中查看该文件内容
- vi/vim  对一个文件进行编辑，如果没有该文件则创建
> 例如：vi log.txt

- ">"  cat log.txt > log.txt.backup
>定向输出文件
    - 如果该文件存在就将原来的清空，如果不存在就创建

- ">>"  cat log.txt >> log.txt.backup
> 定向追加输出文件,如果该文件存在就将原来的文件末尾追加，如果不存在就创建 

- du -h  
> 查看文件/文件夹大小    
    
- history [num]
> 显示历史命令，num表示显示多少条

- cat filename
> 显示该文件全部内容，cat f1 f2 > f3 将f1，f2文件合并到f3
- touch filename
> 创建一个新的文件

- tail [选项] filename
> 默认显示该文件最后10行
    - tail -f filename 实时刷新
    - tail +num filename 从第num行开始显示
    - tail -num filename 从倒数num行开始显示

- :s/well/good/g 
> vi中替换文字，全局替换，将所有的well替换成good

- find / -name "*名称*"
> 查询所有该名称的文件
    

#### 网页下载
- curl http://www.baidu.com
> 获取网页内容
- curl -O http://***
>下载文件


- wget  wget http://***
> 用于下载

- yum -y install 软件名称
- apt-get install 软件名称

  
#### 系统操作
- top 
> 查看系统cpu、内存等使用情况

- vmstat 1 
> 每秒刷新CPU、IO、memory相关信息

    
- iostat 1 
> 查看io读写 1s刷新一次
- ps -ef | grep 关键字
> 查看是否存在该关键字相关进程


- netstat -apn
> 查看所有应用的ip，端口和状态

- source /etc/profile
> 重新加载系统配置文件，一般修改了里面的配置需要使用该命令

- ifconfig
> 查看本地ip配置信息

- firewall-cmd --state
> centos默认会开启firewall，可以用这个命令查看运行状态
```
service firewall stop/start/restart 停止/启动/重启
firewall-cmd -permanent --add-port=8080-8085/tcp
开启8080-8085之间的端口以tcp的方式连接，然后firewall-cmd --reload重新加载下就可以用了
firewall-cmd -permanent --remove-port=8080-8085/tcp
关闭，也要重启下，但要注意的是怎么开启的要怎么关闭，不能开启一组，但关闭一个，是不允许的
firewall-cmd -permanent --list-ports
查看开启了哪些端口
firewall-cmd -permanent --list-services
查看哪些程序使用了互联网
```

#### 查找
- find [目录] -iname '关键字'
> 在该目录中找到该关键字忽略大小写所有信息

#### 压缩与解压
- zip *.zip file1 file2...
> 将众多文件压缩成一个
- upzip *.zip
> 解压文件
- tar -xzvf *.tar
> 解压该文件并列出详细信息

#### 登录与登出
- ssh 用户名@ip
> 远程登录
- logout
> 退出
- shutdown
> 关机

#### 权限操作
```
r=读取属性     值＝4  
w=写入属性　　  值＝2      
x=执行属性　　  值＝1
```
- chmod 777 文件夹或文件
> 授权这个文件夹或文件读写执行的权限

- chown
 > sudo chown -R user1 /usr/local 对user1用户在这个目录授权读写