# MySQL主从复制

## 介绍

MySQL主从复制是一个异步的复制过程

底层是基于MySQL数据库自带的**二进制日志**功能

就是一台或多台MySQL数据库（**slave，从库**）从另一台唯一MySQL数据库（master，主库）**进行日志的复制然后再解析日志并应用到自身**

最终实现从库数据和主库数据保持一致

MySQL主从复制是自带的功能，无需第三方工具



大致流程：

```txt
主库master ----Data changes----> binlog(二进制日志文件)
从库slave
	-> I/O Thread ==read==> 主库的binlog(二进制日志文件) ==write==> Relaylog(中继日志)
	-> SQL Thread ==解析==> Relaylog(中继日志) ==replay==> 主库的数据变化同步到了从库
```

分成三步：

* maser将改变记录到**二进制日志文件**（binlog）
* salve将master的binlog拷贝到自身的**中继日志**（relaylog）
* slave重做relaylog中的事件，将主库的改变应用到自身数据库





## 配置

配置条件：至少两台服务器

* master的ip

* slave的ip



配置主库master(Windows)

1. 修改MySQL数据库的配置文件

   * Linux下，配置文件为：/etc/my.cnf

   * Windows下，配置文件为：安装目录下的**my.ini**

   * ```ini
     [mysqld]
     log-bin=mysql-bin  #[必须]启用二进制日志
     server-id=2        #[必须]服务器唯一id
     ```

2. 修改完配置需要重新启动mysql服务

3. 登录mysql，执行SQL语句

   ```sql
   #mysql 8.0 版本需要如下
   create user 'slave'@'从库机ip' identified by 'root';
   grant replication slave on *.* to 'slave'@'从库机ip';
   ```

   ：创建一个用户，且给这个用户授予REPLICATION SLAVE权限。常用于建立复制时所需要用到的用户权限。slave必须被master授予权限，具有该权限的用户才可以从主库复制

4. 执行SQL

   ```sql
   show master status; #查看master的状态 一般执行完之后需要使用结果的话就不要进行其他操作 不然会改变值
   ```

   记录结果中的File和Position的值

【注】Windows作为主库记得防火墙新建一个规则 开放3306端口可访问

![image-20230112193645316](.\images\image-20230112193645316.png)



配置从库slave(Linux)

1. 修改MySQL数据库的配置文件

   * Linux下，配置文件为：/etc/my.cnf

   * Windows下，配置文件为：安装目录下的**my.ini**

   * ```ini
     [mysqld]
     #服务器唯一id
     server-id=92
     # 启用中继日志
     relay-log=mysql-relay  
     ```

2. 修改完配置需要重新启动mysql服务：systemctl restart mysqld

3. 登录mysql，执行SQL语句

   ```sql
   #绑定主库 最后使用的值就是刚刚 show master status; 查询出来的两个值
   #主机地址应该填自己VMnet8中的Ipv4地址
   change master to master_host='192.168.88.1',master_user='slave',master_password='root',master_log_file='binlog.000182',master_log_pos=678;
   
   #开启slave
   start slave;
   ```

4. 执行SQL

   ```sql
   show slave status\G;
   ```

5. Slave_IO_Running: Yes
   Slave_SQL_Running: Yes

   即为成功



















