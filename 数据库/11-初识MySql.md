# 初识MySql

> 写于：2018-09-18


### 基本使用
- 创建库
    ```
    create database dbname
    ```
- 删库
    ```
    drop database dbname
    ```
- 插入数据
    ```
    insert into table_name(field1...) values(v1...)
    ```
- 删除数据
    ```
    update table_name set field1=v1 where id=1
    ```
- 删除数据
    ```
    delete from table_name where id=1
    ```
- left join
```
左连接，返回左表所有数据，即使另外一张表没有匹配的数据

create table `person`(
	`personId` int not null,
	`firstName` varchar(100) not null,
	`lastName` varchar(100) not null,
	primary key (`personId`)

);

create table `address`(
	`addressId` int not null,
	`personId` varchar(100) not null,
	`city` varchar(100) not null,
	`state` varchar(100) not null,
	primary key (`addressId`)
);

select FirstName, LastName, City, State
from Person left join Address
on Person.PersonId = Address.PersonId;
```
**group by having**
```
group by根据什么分组，having相当于是where，不过having可以根据聚合函数来判断，而where不行
select a.s_id,a.s_name,avg(b.s_score) as avg_score
from student a join score b on a.s_id = b.s_id
GROUP BY a.s_id HAVING avg(b.s_score) < 60
```

### 主从复制
**原理**

binlog,mysql会将所有的sql语句存储在这个二进制日志中（这个日志默认是不开启的），然后将和这个日志文件拷贝到另外一个mysql服务器中， 从服务器就会开启一个线程从二进制文件中读取数据，同步到自己的relay log中，将这些sql语句还原，从而实现同步。

![这里缺一张图片](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/14-mysql%E9%9B%86%E7%BE%A4%E3%80%81%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6/01-msyql%E4%B8%BB%E4%BB%8E%E5%90%8C%E6%AD%A5%E5%8E%9F%E7%90%86.png)

**配置说明**

这里准备2台服务器，一台是192.168.244.134做主机，一台是192.168.244.136做从机

mysql5.7.13(rpm安装)

centos 7

**mysql安装**

参考另一篇笔记

==**先配置主机104**==

**修改134mysql配置开启日志文件**

命令：`vi /etc/my.cnf`
```
添加：
log-bin=mysql-bin
server-id=1 #这个server-id    # 是唯一的且只能是数字
innodb-file-per-table =ON     # 表优化
```

**登入mysql，查看主机状态**

`mysql -uroot -p`

`show master status;`

记录下主机状态中的最新一条记录的File和Position

![image](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/14-mysql%E9%9B%86%E7%BE%A4%E3%80%81%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6/02-%E6%9F%A5%E7%9C%8B%E4%B8%BB%E6%9C%BAbinlog%E5%90%8D%E7%A7%B0.png)

---
```
**主机数据备份（可选）**

`mysqldump --master-data=2 --single-transaction --routines --triggers --events -uroot -p test_db > bak_imooc.sql`

如果是生产环境，可以是用这种方式。使用root用户将test_db数据库备份到bak_test.sql中，同时保存触发器，存储过程，事件，开启事物保证数据一致性

**将bak_test.sql拷贝到136服务器上（可选）**

`scp ./bak_test.sql root@ip:/路径`

**在136服务器导入数据库（可选）**

和之前导入操作一样
```
---

**在134主机中创建repl用户，让它有权利从这里复制**

`grant replication slave, replication client on *.* to 'repluser1'@'192.168.244.%' identified by 'rootroot';`

可以访问192.168.244网段的服务器


**刷新权限**

`flush privileges`

**134主机配置好了，要看下3306端口是否允许外部访问**

==**配置从机**==

**开启relay日志和server-id**

命令：`vi /etc/my.cnf`
```
relay-log=relay-log
relay-log-index=relay-log.index
server-id=2
innodb-file-per-table =ON
```

**重启并登录mysql**

`service mysqld restart`

`mysql -uroot -p`


**指定主机节点**

`change master to master_host='192.168.244.134',master_user='root',master_password='rootroot',master_log_file='mysql-bin.000001', MASTER_LOG_POS=154;`

在从机135mysql中指定主机节点，主机ip,用户名，密码和binlog文件

**开启从机**

`start slave;`

**查看从机状态**

`show slave status \G;`

**启动成功**

![启动成功的图](https://raw.githubusercontent.com/liueleven/study/master/%E5%9B%BE%E5%BA%93/14-mysql%E9%9B%86%E7%BE%A4%E3%80%81%E4%B8%BB%E4%BB%8E%E5%A4%8D%E5%88%B6/04-%E5%90%AF%E5%8A%A8%E6%88%90%E5%8A%9F.png)

==**一些问题**==

在测试的时候直接克隆，导致mysql uuid重复没有启动成功，可以在/var/lib/mysql/auto.cnf修改

如果遇到问题直接看mysql日志/var/log//mysqld.log，一般都能解决了
****



### sql优化
[MySql优化总结](数据库/24-MySql优化总结.md)


### MySQL常用命令
1.登录：`mysql -uroot -p`

2.直接执行sql语句：`mysql -uroot -p -e"create database test_db"`
```
-e 后面可以写sql，直接操作sql语句不需要进入mysql，例如：
mysql -uroot -p -e"test_db < test.sql"  # 将test.sql导入数据库
```

3.远程连接mysql：`mysql -h ip -p 端口 -u 用户名 -p 密码`

4.修改密码 `SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');`

5.允许远程访问
```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;

flush privileges
```