# Sharding JDBC

## 介绍

Sharding JDBC是一个轻量级的Java框架，在Java的jdbc层提供额外的服务。

使用客户端直连数据库，以jar包的形式提供服务，我需额外的部署和依赖，可以看做增强版的jdbc驱动，完全兼容jdbc和各种orm框架



特点

* 适用于任何基于jdbc的orm框架：如mybatis
* 支持任何第三方的数据库里连接池：如Druid
* 支持任意实现jdbc规范的数据库：如MySQL



坐标依赖

```xml
<dependency>
	<groupId>org.apache.shardingsphere</groupId>
    <artifactId>sharding-jdbc-spring-boot-starter</artifactId>
    <version>4.0.0-RC1</version>
</dependency>
```

------



## 使用

使用Sharding-JDBC实现读写分离步骤：

1. 导入Sharding-JDBC依赖坐标

2. 在配置文件中配置读写分离规则

3. 在配置文件中配置允许bean定义覆盖配置项

   配置文件详情：

   ```yml
   server:
     port: 8080
   
   spring:
     #配置Sharding JDBC
     shardingsphere:
       datasource:
         #配置主从数据源名称 上下要对应 这里设置的是什么 下面就是什么
         names:
           master,slave
         #主数据源 - 名称为上面设置的master
         master:
           type: com.alibaba.druid.pool.DruidDataSource
           driver-class-name: com.mysql.cj.jdbc.Driver
           url: jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&userSSL=false&serverTimezone=Asia/Shanghai
           username: root
           password: '020920'
         # 从数据源
         slave:
           type: com.alibaba.druid.pool.DruidDataSource
           driver-class-name: com.mysql.cj.jdbc.Driver
           url: jdbc:mysql://192.168.88.92:3306/test?characterEncoding=utf-8&userSSL=false&serverTimezone=Asia/Shanghai
           username: root
           password: Wyh0920^
       masterslave:
         # 读写分离配置 - 负载均衡
         load-balance-algorithm-type: round_robin #轮询
         # 最终的数据源名称
         name: dataSource
         # 主库数据源名称
         master-data-source-name: master
         # 从库数据源名称列表，多个逗号分隔
         slave-data-source-names: slave
       props:
         sql:
           show: true #开启SQL显示，默认false
     main:
       allow-bean-definition-overriding: true
   mybatis-plus:
     configuration:
       #在映射实体或者属性时，将数据库中表名和字段名中的下划线去掉，按照驼峰命名法映射
       map-underscore-to-camel-case: true
       log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
     global-config:
       db-config:
         id-type: ASSIGN_ID
   ```



框架会去检测配置文件 然后根据SQL语句的类型 去操作相应配置好的主从数据库

------

> ©iWyh2