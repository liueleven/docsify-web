# Redis 初识

> 写于：2018-07-23
---
### 是什么？
是完全免费开源的，一个高性能的key-value非关系型数据库。NoSQl(not only sql):泛指非关系型数据，库常见的有MangoDB，Redis
	
特点：易扩展，灵活的数据模型，高可用，高性能

### 能干嘛？
- 支持数据持久化；
- 不仅支持key-value数据结果，还支持其他的set，list等丰富的数据结构；
- 支持数据的备份，即master-slave模式的数据备份；
- 内存存储和持久化，取最新N个数据的操作，发布、订阅消息系统，定时器、计数器
- 读写快！redis将数据读读到内存中，通过异步的方式将数据持久化到硬盘

### redis命令工具redis-cli
启动：./redis-cli

如果有密码连接：auth 密码

测试是否启动：ping,返回pong则是成功

连接远程redis：./redis-cli -h ip -p port -a password

### 怎么用？
和mysql一样需要安装，然后在java中引入依赖包，因为它实现了java的一些对象，方便我们操作
和mysql一样需要安装，然后在java中引入依赖包，因为它实现了java的一些对象，方便我们操作

	
### 传统数据库mysql和redis对比
    mysql：	ACID
		A:	Atomicity	原子性
			也就是说事物里的所有操作要么都成功，要么都不成功。例如：A转给了B100元，A转成功了，但是B接收失败，那么就按照原子性，就会
			回滚所有操作。
		C:	Consistency	一致性
		I：	Isolation	独立性
			并发事物之间不影响
		D:	Durability	持久性
	redis：	CAP
		C:	Consistency	强一致性
		A：	Availabity	可用性
		P：	Partition tolerance  分区容错性
		一个分布式系统一般3个只能满足2个条件，AC,CP,AP，然而P又是分布式系统必须满足的条件，传统的是CA，分布式中AP是大多数的选择
		
### 安装redis
redis安装步骤：

	1、安装Linx虚拟机，系统CentOS，SSH客户端SecureCRT
	2、Linx在线安装gcc，yum install -y gcc-c++，一路选 y
	3、下载redis.tar.gz安装包、上传到linux上面
	4、解压文件 tar -axvf redis.tar.gz
	5、进入redis目录，编译 make
	6、安装、指定安装目录 make PREFIX=/usr/local/redis install
	7、进入安装完成的目录 cd /usr/local/redis
	8、返回解压安装路径
	9、拷贝redis.conf  cp redis.conf /usr/local/redis
	11、cd /usr/local/redis 修改配置文件 vim redis.conf，进入编辑模式
	12，daemonize no 改为 yes  #守护进程，后台运行
	13、启动redis  redis-server ./redis.conf    启动客户端./redis-cli -p  6379
		注意：可以有多个conf配置文件，就意味着有多个redis，端口也可以另外起
	14、测试启动 ps -f | grep -i redis、默认端口6379  输入ping  返回pong则成功
	15、停止启动  shutdown
	
### redis五种常见的数据结构

- String 
    > 类似Java string，一个键最大能存储512MB,set name "zhangsan" get name
- list
    > 类似Java list，lpush l1 v1, lpush l1 v2, lrange l1 0 10
- zset（sorted set）
    > 有序的set，通过一个浮点数来排序,zadd score 91 xiaoming, zrangebyscore score 0 1000
- hash
    > 类似Java map，一个key，val的集合，hmset map k1 v1，hget map k1
- set
    > 类似Java set，成功返回1，失败返回0，sadd k1 v1，smembers k1

### redis常见操作指令
	1.select：				总共有16个库，下标从0-15，使用select 下标   进行切换数据库
	2.dbsize：				显示有多少个值
	3.keys：  				keys * 显示当前库所以key，keys k? 模糊查询出所有k开头的key
	4.flushdb：				清除当前数据库，flushall清除所有数据库
	5.move：				move k3 2 将k3键值对移到数据2中
	6.exists：  			exists key 判断某个key是否存在
	7.expire：  			expire key 秒  设置该key多少秒后过期
	8.type：				type key 查看该key多少秒后过期
	9.ttl：					ttl key查看还有多少秒后过期，-1永不过期，-2已过期
	10.config get name		读取该name的相关配置
	11.config set           设置该name的配置
	
5.redis redis.conf配置文件[具体介绍](https://www.cnblogs.com/kreo/p/4423362.html) 
	
6.持久化

	两种持久化方式：
	1.RDB（Redis DataBase）
		redis是一个数据库，会以一定的时间间隔内将数据写到磁盘中，持久化，当断电或异常时，再下次复重新从磁盘中读取数据文件
		默认的文件名是dump.rdb,但是如果上一次是异常关闭redis的话，redis会把最后的数据强制读到磁盘中，但是这次数据有可能是空的
		也就是说，用reb的方式可能造成最后一次数据丢失
		一定的时间间隔：其实是一个触发条件，文档有说明15min改1次，5min改10次...这些可以自定义修改的，但是redis异常关闭时，这个是强制保存
		到磁盘中的，save命令也可以触发保存机制
		优势：适合大规模的数据恢复，速度很快；有2中方式，一种是直接放在目录，只用是CONFIG GET 目录（具体百度）
		劣势：最后一次保存如果发生异常，会丢失数据
	2.AOF（Append Only File）
		aof是个文件，redis每秒读取数据保存到aof文件中，这个文件会很多，并且增加io的复旦，但是文件达到一个值时，redis会将它压缩，
		整体情况是要从配置文件中开启，然后aof文件会保存redis所有的操作，然后待要恢复的时候，再原封不动的执行该文件的内容
		达到恢复数据的功能，aof比rdb保存数据更加完整，当开启2中模式的话，redis会默认对aof的数据先加载恢复
		
7.redis同样也支持事物

8.redis消息订阅功能--可以充当消息中间件，但是一般不用它，仅仅只是用它作为分布式数据库缓存而已，有专门的消息中间件可以用

9.redis主从复制（master-slave）
	
    1.info replication     		查看当前redis角色信息，是主机还是从机
    2.slaveof ip port			将当前redis作为从机，指定主机ip和端口
    哨兵机制 sentinel.conf		在这个配置文件中配置监听策略，当master挂了，根据配置文件从slave中选出master，如果这时原来master恢复了，它也只能是个slave了
									
10.SpringBoot集成了Redis
```
springboot中使用redis引入依赖
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

@Autowired
private StringRedisTemplate redisTemplate;

StringRedisTemplate对redis做了封装，直接使用即可，可以参考api
```
	
	
	