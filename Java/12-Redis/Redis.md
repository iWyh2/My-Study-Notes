# Redis

![](https://dwglogo.com/wp-content/uploads/2017/12/Redis_Logo-768x433.png)

# 一. 基础篇

## 1. 初识Redis

### 1.1 认识NoSQL

|          |                             SQL                              |                        NoSQL                        |
| :------: | :----------------------------------------------------------: | :-------------------------------------------------: |
| 数据结构 |                     结构化（Structured）                     |                      非结构化                       |
| 数据关联 |                     关联的（Relational）                     |                      无关联的                       |
| 查询方式 |                           SQL查询                            |                      非SQL查询                      |
| 事务特性 | ACID（原子性Atomicity 一致性Consistency 隔离性Isolation 持久性Durability） |               BASE（基本一致 无事务）               |
| 存储方式 |                             磁盘                             |                        内存                         |
|  扩展性  |                             垂直                             |                        水平                         |
| 使用场景 |   1）数据结构固定 2）相关业务对数据安全性，一致性要求较高    | 1）数据结构固定 2）安全性，一致性要求不高 3）性能高 |

NoSQL的非结构化：

1. 键值对
2. Document（文档类型）
3. 列类型
4. Graph

### 1.2 认识Redis

Redis（Remote Dictionary Server）远程词典服务器，诞生于2009年，是一个**基于内存的键值型NoSQL数据库**

特性：

* 键值型，value支持多种不同的数据结构，功能丰富
* 单线程，每个命令具备原子性
* 低延迟，速度快（基于内存，IO多路复用）
* 支持数据持久化
* 支持主从集群，分片集群
* 支持多语言客户端

### 1.3 安装Redis

大多数企业都是基于Linux服务器来部署项目，而且Redis官方也没有提供Windows版本的安装包。因此基于Linux系统来安装Redis.

此处选择的Linux版本为CentOS-7

参考：[Redis官网](https://redis.io/)



[**单机安装Redis**]

安装Redis依赖

Redis是基于C语言编写的，因此首先需要安装Redis所需要的gcc依赖：

```sh
yum install -y gcc tcl
```

上传安装包并解压

然后将资料提供的Redis安装包上传到虚拟机的任意目录：

![image-20211211071712536](.\imgs\image-20211211071712536.png)

例如，我放到了/usr/local/src 目录：

![image-20211211080151539](.\imgs\image-20211211080151539.png)

解压缩：

```sh
tar -xzf redis-6.2.6.tar.gz
```

解压后：

![image-20211211080339076](.\imgs\image-20211211080339076.png)

进入redis目录：

```sh
cd redis-6.2.6
```

运行编译命令：

```sh
make && make install
```

如果没有出错，应该就安装成功了

默认的安装路径是在 `/usr/local/bin`目录下：

![image-20211211080603710](.\imgs\image-20211211080603710.png)

该目录已经

默认配置到环境变量，因此可以在任意目录下运行这些命令。其中：

- redis-cli：是redis提供的命令行客户端
- redis-server：是redis的服务端启动脚本
- redis-sentinel：是redis的哨兵启动脚本



[**Redis启动**]

redis的启动方式有很多种，例如：

- 默认启动
- 指定配置启动
- 开机自启

【默认启动】

安装完成后，在任意目录输入redis-server命令即可启动Redis：

```shell
redis-server
```

如图：

![image-20211211081716167](.\imgs\image-20211211081716167.png)

这种启动属于`前台启动`，会阻塞整个会话窗口，窗口关闭或者按下`CTRL + C`则Redis停止。不推荐使用。

【指定配置启动】

如果要让Redis以`后台`方式启动，则必须修改Redis配置文件，就在我们之前解压的redis安装包下（`/usr/local/src/redis-6.2.6`），名字叫【redis.conf】：

![image-20211211082225509](.\imgs\image-20211211082225509.png)

我们先将这个配置文件备份一份：

```shell
cp redis.conf redis.conf.bck
vim redis.conf
```

然后修改redis.conf文件中的一些配置：

```properties
# 允许访问的地址，默认是127.0.0.1，会导致只能在本地访问。修改为0.0.0.0则可以在任意IP访问，生产环境不要设置为0.0.0.0
bind 0.0.0.0
# 守护进程，修改为yes后即可后台运行
daemonize yes 
# 密码，设置后访问Redis必须输入密码
requirepass 020920
```

Redis的其它常见配置：

```properties
# 监听的端口
port 6379
# 工作目录，默认是当前目录，也就是运行redis-server时的命令，日志、持久化等文件会保存在这个目录
dir .
# 数据库数量，设置为1，代表只使用1个库，默认有16个库，编号0~15
databases 1
# 设置redis能够使用的最大内存
maxmemory 512mb
# 日志文件，默认为空，不记录日志，可以指定日志文件名
logfile "redis.log"
```

启动Redis：

```sh
# 进入redis安装目录 
cd /usr/local/src/redis-6.2.6
# 启动
redis-server redis.conf
```

停止服务：

```sh
# 利用redis-cli来执行 shutdown 命令，即可停止 Redis 服务，
# 因为之前配置了密码，因此需要通过 -u 来指定密码
redis-cli -u 020920 shutdown
```

【开机自启】

我们也可以通过配置来实现开机自启。

首先，新建一个系统服务文件：

```shell
vi /etc/systemd/system/redis.service
```

内容如下：

```conf
[Unit]
Description=redis-server
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/bin/redis-server /usr/local/src/redis-6.2.6/redis.conf
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

然后重载系统服务：

```sh
systemctl daemon-reload
```

现在，我们可以用下面这组命令来操作redis了：

```sh
# 启动
systemctl start redis
# 停止
systemctl stop redis
# 重启
systemctl restart redis
# 查看状态
systemctl status redis
```

执行下面的命令，可以让redis开机自启：

```shell
systemctl enable redis
```



[**Redis客户端**]

安装完成Redis，我们就可以操作Redis，实现数据的CRUD了。这需要用到Redis客户端，包括：

- 命令行客户端
- 图形化桌面客户端
- 编程客户端

【Redis命令行客户端】

Redis安装完成后就自带了命令行客户端：redis-cli，使用方式如下：

```shell
redis-cli [options] [commonds]
```

其中常见的options有：

- `-h 127.0.0.1`：指定要连接的redis节点的IP地址，默认是127.0.0.1
- `-p 6379`：指定要连接的redis节点的端口，默认是6379
- `-a 123321`：指定redis的访问密码 

其中的commonds就是Redis的操作命令，例如：

- `ping`：与redis服务端做心跳测试，服务端正常会返回`pong`

不指定commond时，会进入`redis-cli`的交互控制台：

![image-20211211110439353](.\imgs\image-20211211110439353.png)

【图形化桌面客户端】

GitHub上的大神编写了Redis的图形化桌面客户端，地址：https://github.com/uglide/RedisDesktopManager

不过该仓库提供的是RedisDesktopManager的源码，并未提供windows安装包。

在下面这个仓库可以找到安装包：https://github.com/lework/RedisDesktopManager-Windows/releases

![image-20211211111351885](.\imgs\image-20211211111351885.png)

在资料中可以找到Redis的图形化桌面客户端：

![image-20211214154938770](.\imgs\image-20211214154938770.png)

解压缩后，运行安装程序即可安装：

![image-20211214155123841](.\imgs\image-20211214155123841.png)

安装完成后，在安装目录下找到rdm.exe文件：

![image-20211211110935819](.\imgs\image-20211211110935819.png)

双击即可运行：

![image-20211214155406692](.\imgs\image-20211214155406692.png)

点击左上角的`连接到Redis服务器`按钮：

![image-20211214155424842](.\imgs\image-20211214155424842.png)

在弹出的窗口中填写Redis服务信息：

![image-20211211111614483](.\imgs\image-20211211111614483.png)

点击确定后，在左侧菜单会出现这个链接：

![image-20211214155804523](.\imgs\image-20211214155804523.png)

点击即可建立连接了：

![image-20211214155849495](.\imgs\image-20211214155849495.png)

Redis默认有16个仓库，编号从0至15。通过配置文件可以设置仓库数量，但是不超过16，并且不能自定义仓库名称。

如果是基于redis-cli连接Redis服务，可以通过select命令来选择数据库：

```sh
# 选择 0号库
select 0
```

## 2. Redis常见命令

### 2.1 五种常见数据结构

| 基本类型  |          样例          |
| :-------: | :--------------------: |
|  String   |      Hello World       |
|   Hash    | {name: "wyh", age: 21} |
|   List    |   [A -> B -> C -> C]   |
|    Set    |       {A, B, C}        |
| SortedSet |   {A: 1, B: 2, C: 3}   |

| 特殊类型 |        样例         |
| :------: | :-----------------: |
|   GEO    | {A: (120.3, 30.5)}  |
|  BitMap  | 0110110101110101011 |
| HyperLog | 0110110101110101011 |

可以在[官方帮助文档](https://redis.io/commands)查询所有命令

### 2.2 通用命令

通用命令是部分数据类型都可以执行的命令，可以通过`help [命令]`在命令行查看命令的具体用法

* keys：查看符合模板的所有key（不建议在生产环境使用）
* del：删除指定的key（可以删除多个）
* exists：判断key是否存在（可以判断多个）
* expire：给一个key设置有效期，到期后自动删除
* ttl：查看一个key的剩余有效期

### 2.3 不同数据结构的操作命令

[**String类型**]

value是字符串，根据各式不同分为：

1. string字符串
2. int整数类型，可自增自减
3. float浮点类型，可自增自减

虽然各式不同，但是底层都是字节数组，字符串存储最大空间为512M

常用命令：

* set：添加或修改已经存在的String键值对
* get：根据key获取String的value
* mset：批量添加多个String键值对
* mget：根据多个key获取多个String的value
* incr：让一个整型的key的value自增1
* incrby：让一个整型的key的value自增指定步长（比如可以自增2：incrby num 2，设为负数就可以自减）
* incrbyfloat：让一个浮点型的key的value自增指定步长
* setnx：添加一个String键值对，前提是该键值对不存在，否则不执行（只能添加）
* setex：添加一个String键值对并指定有效期

key唯一，但是可以拼接区分key，比如使用（项目名:业务名:类型:id）进行**分级存储**，用**冒号隔开**

同时value如果是一个Java对象，可以将对象序列化为JSON字符串后存储（**添加JSON字符串需要打单引号**）

|       KEY       |                  VALUE                   |
| :-------------: | :--------------------------------------: |
|  iWyh2:User:1   |     {"id":1, "name":"wyh", "age":21}     |
| iWyh2:Product:1 | {"id":1, "name":"iPhone", "price":12222} |



[**Hash类型**]

也叫散列，value是一个无序字典，类似于Java的HashMap

可以**将Java对象的每个字段独立存储，可以针对单个字段进行CRUD**

hashKey -> field：value

常用命令：

* hset key field value：添加或修改已经存在的Hash类型的key的field值
* hget key field：获取一个Hash类型key的field的value
* hmset：批量添加Hash类型key的多个field的value
* hmget：批量获取Hash类型key的多个field的value
* hgetall：获取一个Hash类型key的所有field的value
* hkeys：获取一个Hash类型key的所有field
* hvals：获取一个Hash类型key的所有field的value（无field）
* hincrby：让一个Hash类型的key的field的value自增指定步长
* hsetnx：添加一个Hash类型的key的field值，前提是field值不存在，否则不执行（只能添加）



[**List类型**]

可以看做一个双向链表，可以支持正向检索和反向检索。与Java的LinkedList类似（**有序**，可以重复，插入删除快，查询一般）

用来存储有序数据

常用命令：

* lpush key element：向列表左侧插入一个或多个元素
* lpop key：移除并返回列表左侧的第一个元素，没有则返回nil（可以指定返回左侧多少个元素）
* rpush key element：向列表右侧插入一个或多个元素
* rpop key：移除并返回列表右侧的第一个元素，没有则返回nil（可以指定返回右侧多少个元素）
* lrange key start end：返回一段范围内的所有元素
* blpop，brpop：与lpop和rpop类似，但在没有元素时会等待，不会返回nil，相当于阻塞了redis线程（可以指定超时时间）



[**Set类型**]

与Java的HashSet类似，可以看做一个value可以为null的HashMap。也是一个hash表（无序，不重复，查找快，支持交集并集差集）

常用命令：

* sadd key member：向set中添加一个或多个元素
* srem key member：移除set中一个或多个指定元素
* scard key：返回set的元素个数
* sismember key member：判断一个元素是否存在于一个set类型的key中
* smembers：获取set中所有元素
* sinter key1 key2：获取多个set之间的交集
* sdiff key1 key2：获取多个set之间的差集
* sunion key1 key2：获取多个set之间的并集



[**SortedSet类型**]

是一个可排序Set，与Java的TreeSet类似，SortedSet的每个元素都有一个score属性，基于score对元素排序

底层是跳表加哈希表（可排序，不重复，查询快）常用于实现排行榜

常用命令：

* zadd key score member：添加一个或多个元素到SortedSet，如果存在就更新其score值
* zrem key member：删除SortedSet中的一个指定元素
* zscore key member：获取SortedSet指定元素的score值
* zrank key member：获取SortedSet指定元素的排名
* zcard key：获取SortedSet的元素个数
* zcount key min max：统计score值在给定范围内的所有元素个数（给的是score值范围）
* zincrby key increment member：让SortedSet中的指定元素自增指定步长increment
* zrange key min max：按照score值排序后，获取指定排名范围内的元素（给的是排名范围）
* zrangebyscore key min max：按照score排序后，获取指定score范围内的元素
* zdiff，zinter，zunion：获取多个SortedSet的差集，交集，并集

【注】所有的排名都是**默认升序**的，要**降序**则在命令的z后面加上**rev**即可（z**rev**rank key member）

## 3. Redis的Java客户端

Redis官网提供了各种语言的客户端：[点击跳转](https://redis.io/clients)

|  客户端  |                            优缺点                            |
| :------: | :----------------------------------------------------------: |
|  Jedis   | 以Redis命令作为方法名称，学习成本低，简单实用。但Jedis实例线程并不安全，多线程环境需要基于线程池使用 |
| Lettuce  | 基于Netty实现，支持同步，异步，响应式编程方式，线程安全，支持Redis哨兵模式，集群模式，管道模式 |
| Redisson | 基于Redis实现的分布式，可伸缩的Java数据结构集合，包含了Map，Queue，Lock，Semaphore，AtomicLong等 |

### 3.1 Jedis客户端

[Jedis官网](https://github.com/redis/jedis)

[**快速入门**]

1. 引入依赖

   ```xml
   <dependency>
       <groupId>redis.clients</groupId>
       <artifactId>jedis</artifactId>
       <version>3.7.0</version>
   </dependency>
   ```

2. 建立连接

   ```java
   	@BeforeEach//测试方法之前的初始化方法
       void setUp() {
           //建立连接
           jedis = new Jedis("192.168.88.92",6379);
           //输入密码
           jedis.auth("020920");
           //选择库 默认为0库
           jedis.select(0);
       }
   ```

3. 测试String

   ```java
   	@Test
       void StringTest() {
           String result = jedis.set("today", "2023/2/18");
           System.out.println(result);
           result = jedis.get("today");
           System.out.println(result);
       }
   ```

4. 释放资源

   ```java
   	@AfterEach//测试方法之后的收尾方法
       void tearDown() {
           if (jedis != null) {
               //释放资源
               jedis.close();
           }
       }
   ```



[**Jedis线程池**]

Jedis本身线程不安全，用Jedis连接池代替Jedis直连

用一个工具类直接创建Jedis线程池

```java
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;
    static {
        //配置连接池
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxTotal(8);//最大连接数
        jedisPoolConfig.setMaxIdle(8);//最大空闲线程
        jedisPoolConfig.setMinIdle(0);//最小空闲线程
        jedisPoolConfig.setMaxWaitMillis(1000);//ms
        jedisPool = new JedisPool(
                jedisPoolConfig,/*线程池配置*/
                "192.168.88.92",/*host*/
                6379,/*端口*/
                1000,/*超时时间*/
                "020920"/*Redis密码*/
        );
    }
    public static Jedis getJedis() {
        return jedisPool.getResource();
    }
}
```

### 3.2 SpringDataRedis客户端

SpringData是Spring中的数据操作的模块，包含对各种数据库的集成

对Redis的集成模块叫做：**SpringDataRedis**

[SpringDataRedis官网](https://spring.io/projects/spring-data-redis)

* 提供了对不同Redis客户端的整合（**兼容Jedis和Lettuce**）
* **提供了RedisTemplate统一API来操作Redis**
* 支持Redis的发布订阅模型
* 支持Redis哨兵和Redis集群
* 支持基于Lettuce的响应式编程
* 支持基于JDK，JSON，字符串，Spring对象的数据序列化及反序列化
* 支持基于Redis的JDKCollection实现

RedisTemplate工具类封装了各种对Redis的操作，并且将不同数据类型的操作API封装到了不同的操作类型对象中

|             API             |   返回值类型    |         说明          |
| :-------------------------: | :-------------: | :-------------------: |
| redisTemplate.opsForValue() | ValueOperations |  操作String类型数据   |
| redisTemplate.opsForHash()  | HashOperations  |   操作Hash类型数据    |
| redisTemplate.opsForList()  | ListOperations  |   操作List类型数据    |
|  redisTemplate.opsForSet()  |  SetOperations  |    操作Set类型数据    |
| redisTemplate.opsForZSet()  | ZSetOperations  | 操作SortedSet类型数据 |
|        redisTemplate        |                 |       通用命令        |



[**快速入门**]

1. 引入依赖

   ```xml
   <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-data-redis</artifactId>
           </dependency>
           <!--线程池依赖-->
           <dependency>
               <groupId>org.apache.commons</groupId>
               <artifactId>commons-pool2</artifactId>
           </dependency>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <optional>true</optional>
           </dependency>
       </dependencies>
   ```

2. 编写配置

   ```yml
   spring:
     redis:
       host: 192.168.88.92
       port: 6379
       password: "020920" #纯数字密码加上引号，yml才会正确读取
       database: 0        #默认0号数据库 可以不配置
       lettuce:
         pool:
           max-active: 8
           max-idle: 8
           min-idle: 0
           max-wait: 1000
   ```

3. 简单测试

   ```java
   @SpringBootTest
   public class RedisTest {
       @Autowired
       private RedisTemplate redisTemplate;
       @Test
       public void StringTest() {
           redisTemplate.opsForValue().set("name","wyh");
           Object name = redisTemplate.opsForValue().get("name");
           System.out.println(name);
       }
   }
   ```



[**问题解析**]

RedisTemplate可以接收任意Object作为值写入Redis，但是在写入之前会将Object序列化为字节数组，且默认采用JDK的序列化，得到的结果会变成类似于（\xAC\xED\x00\x05t\x00\x03）的字节数组形式，可读性差，且占用内存

所以我们可以设置key和value的序列化实例，让它不采用JDK的序列化

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        //创建RedisTemplate对象
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        //设置连接工厂
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //创建JSON序列化工具（需要有相关jackson依赖）
        GenericJackson2JsonRedisSerializer jsonRedisSerializer = new GenericJackson2JsonRedisSerializer();
        //设置Key的序列化 - String处理
        redisTemplate.setKeySerializer(RedisSerializer.string());
        redisTemplate.setHashKeySerializer(RedisSerializer.string());
        //设置value的序列化 - JSON处理
        redisTemplate.setValueSerializer(jsonRedisSerializer);
        redisTemplate.setHashValueSerializer(jsonRedisSerializer);
        //返回
        return redisTemplate;
    }
}
```

采用这个方法的序列化存入字符串已经没有问题，但是如果要存入一个Java对象时，序列化无任何问题，但是反序列化时，由于会自动帮我们反序列化为相关Java对象，所以在存入数据时，多了一些无关数据：

```json
{
  "@class": "com.wyh.pojo.User",//此处就是由于需要反序列化而多出的Java对象相关信息 - 字节码文件名称，会占用额外内存
  "name": "wyh",
  "age": 20,
  "sex": "man"
}
```

所以，为了节省内存，我们不会使用JSON序列化器去处理value，而是统一使用String序列化器，要求存储的是String类型的key和value。当需要存储Java对象时，就需要我们手动对Java对象进行序列化和反序列化。也就是在RedisConfig中，对于value的序列化设置，需要设置为RedisSerializer.string()。

幸好，SpringDataRedis为我们提供了一个【**StringRedisTemplate**】，为我们省去了需要设置key和value的String序列化器的步骤，只需要我们手动转JSON即可

```java
@SpringBootTest
public class RedisTest {
    @Autowired
    private StringRedisTemplate stringRedisTemplate;
    private final ObjectMapper objectMapper = new ObjectMapper();//SpringMVC提供的默认的JSON处理工具
    @Test
    public void StringTest() throws JsonProcessingException {
        //准备数据
        User user = new User("iWyh2", 21, "man");
        //先手动序列化
        String UserJson = objectMapper.writeValueAsString(user);
        //再写入Redis
        stringRedisTemplate.opsForValue().set("user:3",UserJson);
        //从Redis获取User的JSON字符串
        UserJson = stringRedisTemplate.opsForValue().get("user:3");
        //再将JSON字符串转为Java对象
        User readValue = objectMapper.readValue(UserJson, User.class);//手动告诉字节码
        System.out.println(readValue);
    }
}
```

------



# 二. 实战篇

案例：【黑马点评】

## 1. 短信登录

### 1.1 导入项目

导入资料中的SQL文件，内含

* tb_user：用户表
* tb_user_info：用户详情表
* tb_shop：商户信息表
* tb_shop_type：商户类型表
* tb_blog：用户日记表（达人探店日记）
* tb_follow：用户关注表
* tb_voucher：优惠券表
* tb_voucher_order：优惠券的订单表

项目部署架构：![](.\imgs\hmdp-1.png)

[**后端项目导入**]

将资料中的【hm-dianping】导入IDEA，启动，访问[http://localhost:8081/shop-type/list]成功即可



[**前端项目部署**]

将资料中的【nginx-1.18.0】放在任意目录，然后在该目录下打开这个文件夹，打开cmd窗口，输入：

```shell
start nginx.exe
```

然后访问[http://127.0.0.1:8080]成功即可

项目整体访问[http://localhost:8080]即可

### 1.2 基于Session实现登录

![](.\imgs\hmdp-2.png)

【发送短信验证码】

```java
/*
	@Service
	@Slf4j
	UserServiceImpl
*/
@Override
public Result sendCode(String phone, HttpSession session) {
    //1.校验手机号
    boolean phoneInvalid = RegexUtils.isPhoneInvalid(phone);
    //不符合就拦截
    if (phoneInvalid) {
         return Result.fail("手机号格式错误!");
     }
    //2.符合就放行 生成验证码
    String code = RandomUtil.randomNumbers(6);
    //3.保存验证码到session
    session.setAttribute("code",code);
    //4.发送验证码 - 模拟
    log.info("通过ALiYun发送成功,验证码:[{}]",code);
    //返回
    return Result.ok();
}
```

【短信验证码登录注册】

```java
@Override
    public Result login(LoginFormDTO loginForm, HttpSession session) {
        String phone = loginForm.getPhone();
        String cacheCode = (String)session.getAttribute("code");
        String formCode = loginForm.getCode();
        //1.校验手机号
        if (RegexUtils.isPhoneInvalid(phone)) {
            return Result.fail("手机号格式错误!");
        }
        //2.校验验证码
        if (formCode == null || cacheCode == null) {
            return Result.fail("验证码为空!");
        }
        if (RegexUtils.isCodeInvalid(formCode)) {
            return Result.fail("验证码格式错误!");
        }
        //3.对比session里的验证码和提交的验证码是否一致，不一致直接返回错误
        if (!cacheCode.equals(formCode)) {
            return Result.fail("验证码错误!");
        }
        //4.一致，那么根据手机号查询用户是否存在
        User user = query().eq("phone", phone).one();
        //5.用户不存在，创建并保存
        if (user == null) {
            user = createUserWithPhone(phone);
        }
        //6.保存用户信息到session
        session.setAttribute("user",user);
        /*
            session都有唯一id
            登录时的cookie带上了这个id
         */
        return Result.ok();
    }
    private User createUserWithPhone(String phone) {
        User user = new User();
        user.setPhone(phone);
        user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX+RandomUtil.randomString(10));
        return user;
    }
```

【校验登录状态】

![](.\imgs\hmdp-3.png)

```java
public class LoginInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //1.获取Session
        HttpSession session = request.getSession();
        //2.获取session中的用户
        UserDTO user = (UserDTO) session.getAttribute("user");
        //3.判断用户是否存在，不存在就拦截
        if (user == null) {
            response.setStatus(401);//401是未授权状态码
            return false;//return false就是拦截
        }
        //5.存在就保存到ThreadLocal
        UserHolder.saveUser(user);
        //放行
        return true;//return true就是放行
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();//移除ThreadLocal中设置的变量，避免内存泄露风险
    } 
}
```

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns(
                        //放行的路径
                        "/user/code",
                        "/user/login",
                        "/blog/hot",
                        "/shop/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/voucher/**"
                );
    }
}
```

### 1.3 集群的Session共享问题

**Session共享问题**：【多台Tomcat并不共享session存储空间】，当请求切换到不同的Tomcat服务器时，会导致数据丢失的问题

session的替代方案应该满足：

* 数据共享
* 内存存储
* 键值对类型

明示：=> 【**Redis**】

### 1.4 基于Redis实现共享Session登录

【短信验证码】

将验证码存入Redis。同时**以用户电话号码作为唯一key**

key - value选择数据结构:

* value：简单的验证码 直接以String存储
* key：手机号，phone:电话号码即可

```java
@Service
@Slf4j
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public Result sendCode(String phone, HttpSession session) {
        //1.校验手机号
        boolean phoneInvalid = RegexUtils.isPhoneInvalid(phone);
        //不符合就拦截
        if (phoneInvalid) {
            return Result.fail("手机号格式错误!");
        }
        //2.符合就放行 生成验证码
        String code = RandomUtil.randomNumbers(6);
        //3.保存验证码到session --> Redis
        //session.setAttribute("code",code);
        stringRedisTemplate.opsForValue().set(RedisConstants.LOGIN_CODE_KEY +phone,code, 5, TimeUnit.MINUTES);
        //4.发送验证码 - 模拟
        log.info("通过ALiYun发送成功,验证码:[{}]",code);
        //返回
        return Result.ok();
    }
}
```

【验证码登录注册，校验登录】

将用户信息存入Redis

key - value数据结构选择:

* value：Hash存储对象信息
* key：以随机的token作为key存储用户信息

同时生成的随机token还要返回给前端，前端对后端的请求需要带上token进行访问（前端代码实现将其存入到请求的session中），后端采取token信息进行校验登录状态

```java
@Service
@Slf4j
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements IUserService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public Result login(LoginFormDTO loginForm, HttpSession session) {
        String phone = loginForm.getPhone();
        String cacheCode = stringRedisTemplate.opsForValue().get(RedisConstants.LOGIN_CODE_KEY+phone);//从Redis中获取
        String formCode = loginForm.getCode();
        //1.校验手机号
        if (RegexUtils.isPhoneInvalid(phone)) {
            return Result.fail("手机号格式错误!");
        }
        //2.校验验证码
        if (formCode == null || cacheCode == null) {
            return Result.fail("验证码为空!");
        }
        if (RegexUtils.isCodeInvalid(formCode)) {
            return Result.fail("验证码格式错误!");
        }
        //3.对比session --> Redis里的验证码和提交的验证码是否一致，不一致直接返回错误
        if (!cacheCode.equals(formCode)) {
            return Result.fail("验证码错误!");
        }
        //4.一致，那么根据手机号查询用户是否存在
        User user = query().eq("phone", phone).one();
        //5.用户不存在，创建并保存
        if (user == null) {
            user = createUserWithPhone(phone);
        }
        //6.保存用户信息到session --> Redis
        //6.1 随机生成token(UUID)
        String token = UUID.randomUUID().toString(true);
        //6.2 将User信息转为Hash Map(UTools)
        UserDTO userDTO = BeanUtil.copyProperties(user, UserDTO.class);//省去一些细节信息 避免安全问题
        Map<String, Object> userMap = BeanUtil.beanToMap(
                userDTO,
                new HashMap<>(),
                CopyOptions.create()
                        .setIgnoreNullValue(true)
                        .setFieldValueEditor((FiledName,FiledValue)->FiledValue.toString())
        );//此处的操作时将UserDTO的id从Long转为String，不然会发生类型转换错误
        //6.3 存到Redis(将User直接用Map存入Redis - putAll），设置有效期
        stringRedisTemplate.opsForHash().putAll(RedisConstants.LOGIN_USER_KEY+token,userMap);
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY+token,30,TimeUnit.MINUTES);
        //session.setAttribute("user",userDTO);
        /*
            session都有唯一id
            登录时的cookie带上了这个id
         */
        //7.返回token给前端，前端的请求需要带上这个token
        return Result.ok(token);
    }
    private User createUserWithPhone(String phone) {
        User user = new User();
        user.setPhone(phone);
        user.setNickName(SystemConstants.USER_NICK_NAME_PREFIX+RandomUtil.randomString(10));
        save(user);
        return user;
    }
}
```

```java
public class LoginInterceptor implements HandlerInterceptor {
    private final StringRedisTemplate stringRedisTemplate;
	//由于这个拦截器由我们自己创建，无法注入StringRedisTemplate，所以使用构造方法，由SpringMVC调用，帮忙注入
    public LoginInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //1.获取Session --> 获取token(在请求头中)
        //HttpSession session = request.getSession();
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            response.setStatus(401);//401是未授权状态码
            return false;//return false就是拦截
        }
        //2.获取session中的用户 --> 根据token从Redis中获取用户信息
        //UserDTO user = (UserDTO) session.getAttribute("user");
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(RedisConstants.LOGIN_USER_KEY + token);
        //3.判断是否获取到user信息
        if (userMap.isEmpty()) {
            response.setStatus(401);//401是未授权状态码
            return false;//return false就是拦截
        }
        //5.存在就保存到ThreadLocal --> 先将从Redis获取的Hash数据转为UserDTO，再保存
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        UserHolder.saveUser(userDTO);
        //6.刷新token有效期
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY + token,30, TimeUnit.MINUTES);
        //放行
        return true;//return true就是放行
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```

Redis代替Session需要考虑的问题：

* 选择合适的数据结构
* 选择合适的key
* 选择合适的存储粒度

【登录拦截器的优化】

![](.\imgs\hmdp-4.png)

其他的一些路径请求不会被拦截，token的有效期不会被刷新，不合理

所以，需要再增加一个拦截器：

```tex

用户 ==请求==> 拦截器1(拦截一切路径)
				1.获取token
				2.查询Redis用户
				3.保存用户信息到ThreadLocal
				4.刷新token有效期
				5.放行 
	==请求==> 拦截器2(拦截需要登录的路径)
				1.查询ThreadLocal的用户信息
				不存在拦截
				存在放行

```

```java
public class TokenInterceptor implements HandlerInterceptor {
    private final StringRedisTemplate stringRedisTemplate;

    //由于这个拦截器由我们自己创建，无法注入StringRedisTemplate，所以使用构造方法，由SpringMVC调用，帮忙注入
    public TokenInterceptor(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //1.获取Session --> 获取token(在请求头中)
        //HttpSession session = request.getSession();
        String token = request.getHeader("authorization");
        if (StrUtil.isBlank(token)) {
            return true;//直接放行
        }
        //2.获取session中的用户 --> 根据token从Redis中获取用户信息
        //UserDTO user = (UserDTO) session.getAttribute("user");
        Map<Object, Object> userMap = stringRedisTemplate.opsForHash().entries(RedisConstants.LOGIN_USER_KEY + token);
        //3.判断是否获取到user信息
        if (userMap.isEmpty()) {
            return true;
        }
        //5.存在就保存到ThreadLocal --> 先将从Redis获取的Hash数据转为UserDTO，再保存
        UserDTO userDTO = BeanUtil.fillBeanWithMap(userMap, new UserDTO(), false);
        UserHolder.saveUser(userDTO);
        //6.刷新token有效期
        stringRedisTemplate.expire(RedisConstants.LOGIN_USER_KEY + token,30, TimeUnit.MINUTES);
        //放行
        return true;//return true就是放行
    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        UserHolder.removeUser();
    }
}
```

```java
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //判断是否需要拦截（ThreadLocal中是否有用户信息）
        if (UserHolder.getUser() == null) {
            //没有就显示未授权并拦截
            response.setStatus(402);
            return false;
        }
        //有就放行
        return true;//return true就是放行
    }
}
```

```java
@Configuration
public class MvcConfig implements WebMvcConfigurer {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new TokenInterceptor(stringRedisTemplate)).order(0);//越小拦截器就先执行
        registry.addInterceptor(new LoginInterceptor())
                .excludePathPatterns(
                        //放行的路径
                        "/user/code",
                        "/user/login",
                        "/blog/hot",
                        "/shop/**",
                        "/shop-type/**",
                        "/upload/**",
                        "/voucher/**"
                ).order(1);
    }
}
```

## 2. 商户查询缓存

### 2.1 什么是缓存

缓存是数据交换的缓冲区，叫做Cache。是**存储数据的临时地方，一般读写性能较高**

![](.\imgs\hmdp-5.png)

![](.\imgs\hmdp-6.png)

使用缓存需要考虑到：**缓存带来的作用和收益大于使用缓存的成本**

### 2.2 添加Redis缓存

![](.\imgs\hmdp-7.png)



【根据id查询店铺缓存】

![](.\imgs\hmdp-8.png)

```java
@Service
public class ShopServiceImpl extends ServiceImpl<ShopMapper, Shop> implements IShopService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    public Result queryByShopId(Long id) {
        //1.从redis查询店铺缓存
        String cacheShopJSON = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //2.判断是否存在
        if (StrUtil.isNotBlank(cacheShopJSON)) {
            //存在直接返回
            Shop shop = JSONUtil.toBean(cacheShopJSON, Shop.class);
            return Result.ok(shop);
        }
        //3.不存在，根据店铺id查询数据库
        Shop shop = getById(id);
        //4.查询不到返回错误
        if (shop == null) {
            return Result.fail("店铺不存在!");
        }
        //5.查询到了就写入redis并返回
        String shopJSON = JSONUtil.toJsonStr(shop);
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,shopJSON);
        return Result.ok(shop);
    }
}
```



【查询店铺类型缓存】

```java
@Service
@Slf4j
public class ShopTypeServiceImpl extends ServiceImpl<ShopTypeMapper, ShopType> implements IShopTypeService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Resource
    private ShopTypeMapper shopTypeMapper;
    @Override
    public Result queryShopTypeList() {
        //1.从redis中查询店铺类型缓存
        String cacheShopTypeJSON = stringRedisTemplate.opsForValue().get("shop:type");
        //2.判断是否存在缓存
        if (StrUtil.isNotBlank(cacheShopTypeJSON)) {
            //存在缓存，直接返回
            List<ShopType> shopTypeList = JSONUtil.toList(cacheShopTypeJSON, ShopType.class);
            return Result.ok(shopTypeList);
        }
        //3.不存在，就查询数据库
        List<ShopType> shopTypeList = shopTypeMapper.selectList(null);
        //4.查询不到就报错给前端
        if (shopTypeList.isEmpty()) {
            log.info("数据库查询为空");
            return Result.fail("服务器异常!");
        }
        //5.查询到了就存入redis，并返回
        String shopTypeJSON = JSONUtil.toJsonStr(shopTypeList);
        stringRedisTemplate.opsForValue().set("shop:type",shopTypeJSON);
        return Result.ok(shopTypeList);
    }
}
```

### 2.3 缓存更新策略

|          |                           内存淘汰                           |                         超时剔除                          |                  主动更新                  |
| :------: | :----------------------------------------------------------: | :-------------------------------------------------------: | :----------------------------------------: |
|   说明   | 不用自己维护，利用Redis的内存淘汰机制（自带的），当内存不足时自动淘汰掉一部分数据。下一次查询时更新缓存 | 给缓存数据添加TTL，到期后自动删除缓存，下次查询时更新缓存 | 编写业务逻辑，在修改数据库的同时，更新缓存 |
|  一致性  |                              差                              |                           一般                            |                     好                     |
| 维护成本 |                              无                              |                            低                             |                     高                     |

业务场景：

* 低一致性需求：使用内存淘汰机制。例如店铺类型的查询缓存
* 高一致性需求：主动更新，并以超时剔除作为兜底方案。例如店铺详情查询的缓存



【**主动更新策略**】：

![](.\imgs\hmdp-10.png)

一般情况下，**由缓存的调用者，在更新数据库的同时更新缓存**

操作数据库和缓存时需要考虑的三个问题：

1. 是删除缓存还是更新缓存？

   ：更新缓存，也就是每次更新数据库都更新缓存，无效写操作居多。删除缓存是更新数据库时让缓存失效，查询时再更新缓存。所以选择**删除缓存**

2. 如何保证缓存与数据库的操作同时成功同时失败（原子性）？

   ：**单体系统，将缓存和数据库操作放在同一个事务中**。分布式系统，利用TCC等分布式事务方案

3. 先操作缓存还是数据库？

   ![](.\imgs\hmdp-11.png)

   ：**先操作数据库，再删除缓存**的风险性更低，可以设置超时剔除作为兜底



【主动更新缓存实战】

修改项目中[ShopController]中的业务逻辑：

* 根据id查询店铺时，如果缓存未命中，则查询数据库，将数据库结果写入缓存，并设置超时时间
* 根据id修改店铺时，先修改数据库，再删除缓存

```java
@Service
public class ShopServiceImpl extends ServiceImpl<ShopMapper, Shop> implements IShopService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Override
    @Transactional
    public Result queryByShopId(Long id) {
        //1.从redis查询店铺缓存
        String cacheShopJSON = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //2.判断是否存在
        if (StrUtil.isNotBlank(cacheShopJSON)) {
            //存在直接返回
            Shop shop = JSONUtil.toBean(cacheShopJSON, Shop.class);
            return Result.ok(shop);
        }
        //3.不存在，根据店铺id查询数据库
        Shop shop = getById(id);
        //4.查询不到返回错误
        if (shop == null) {
            return Result.fail("店铺不存在!");
        }
        //5.查询到了就写入redis并返回 同时设置超时时间 - 30min
        String shopJSON = JSONUtil.toJsonStr(shop);
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,shopJSON,30L, TimeUnit.MINUTES);
        return Result.ok(shop);
    }
    @Override
    @Transactional//单体项目，使数据库修改和缓存更新在一个事务中，出现异常就会一起回滚
    public Result updateShop(Shop shop) {
        Long id = shop.getId();
        if (id == null) {
            return Result.fail("店铺id为空!");
        }
        //先修改数据库
        updateById(shop);
        //再删除缓存
        stringRedisTemplate.delete(RedisConstants.CACHE_SHOP_KEY + shop.getId());
        return Result.ok();
    }
}
```

### 2.4 缓存穿透

【缓存穿透】：是指客户端**请求的数据在缓存和数据库中都不存在**。这样缓存永远都不会生效，这些请求会直接打到数据库上，造成负担

常见的解决方法：

1. 缓存空对象：

   优点：实现简单，维护方便

   缺点：

   * 额外的内存消耗
   * 可能造成短期的不一致

![](.\imgs\redis-nullObject.png)

1. 布隆过滤：

   优点：内存占用少，没有多余的key

   缺点：

   * 实现复杂
   * 存在有误判，不存在一定不存在，存在可能不存在

![](.\imgs\bulongInterceptor.png)

**一般选择缓存空对象**



【解决缓存穿透，缓存商铺数据实战】

![](.\imgs\hmdp-9.png)

```java
	@Override
    public Result queryByShopId(Long id) {
        //1.从redis查询店铺缓存
        String cacheShopJSON = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //2.判断是否存在
        if (StrUtil.isNotBlank(cacheShopJSON)) {
            //存在直接返回
            Shop shop = JSONUtil.toBean(cacheShopJSON, Shop.class);
            return Result.ok(shop);
        }
        //isBotBlank: 对于空字符串"" 会判断为false，不会直接返回，所以需要我们对CacheShopJSON判断，空字符串并不为null
        //也就是判断命中的是否为空字符串
        if (cacheShopJSON != null) {
            return Result.fail("店铺信息不存在!");
        }
        //只要不是 空字符串 和 空对象 那么就是不存在店铺信息 就会执行以下步骤
        //3.不存在，根据店铺id查询数据库
        Shop shop = getById(id);
        //4.查询不到返回错误 --> 数据库未查询到，那么缓存一个空字符串到redis
        if (shop == null) {
            stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,"",2L, TimeUnit.MINUTES);
            return Result.fail("店铺不存在!");
        }
        //5.查询到了就写入redis并返回
        String shopJSON = JSONUtil.toJsonStr(shop);
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,shopJSON,30L, TimeUnit.MINUTES);
        return Result.ok(shop);
    }
```



【缓存穿透的解决方案】：

1. 缓存null值
2. 布隆过滤
3. 增强id的复杂度，避免被猜测到了id规律
4. 做好数据的基础格式校验
5. 加强用户权限校验
6. 做好热点参数的限流

### 2.5 缓存血崩

【缓存雪崩】：是指**在同一时间段内大量的缓存key同时失效**或者**Redis服务宕机**，导致大量请求到达数据库，带来巨大压力

解决方案：

1. 给不同的key的TTL添加随机值（在一个范围内波动，使其不同时失效）
2. 利用Redis集群提高服务的可用性
3. 给缓存业务添加降级限流策略（微服务）
4. 给业务添加多级缓存

### 2.6 缓存击穿

【缓存击穿】：也叫**热点key问题**，就是**一个被高并发访问并且缓存重建业务较复杂的key突然失效**，无数的请求访问会在瞬间给数据库带来巨大的冲击

常见的解决方案：

* 互斥锁：一个线程获取锁去重建缓存，其他线程等待

  ![](.\imgs\redisLOCK.png)

* 逻辑过期：过期就获取锁去开启新线程重建缓存，在重建成功之前，都返回旧数据

  ![](.\imgs\logicTime.png)

| 解决方案 |                   优点                   |                   缺点                   |
| :------: | :--------------------------------------: | :--------------------------------------: |
|  互斥锁  | 没有额外的内存消耗；保证一致性；实现简单 | 线程需要等待，性能受影响；可能有死锁风险 |
| 逻辑过期 |          线程无需等待，性能较好          |  不保证一致性；有额外内存消耗；实现复杂  |



【基于互斥锁解决缓存击穿】

![](.\imgs\hmdp-LOCK.png)

互斥锁简单实现思路：**Redis的String类型key的setnx**命令，只有不存在时才可以设置值，达到互斥效果

```java
	/**
     * 互斥锁方法
     * @param key 传入的key，作为锁
     * @return 获取锁是否成功
     */
    private boolean tryLock(String key) {
        //利用redis的setnx
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(flag);//拆箱，避免出现空指针
    }

    /**
     * 释放锁
     * @param key 传入的锁名
     */
    private void unLock(String key) {
        stringRedisTemplate.delete(key);
    }
```

```java
	public Shop queryWithMutex(Long id) {
        //1.从redis查询店铺缓存
        String cacheShopJSON = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //2.判断是否存在
        if (StrUtil.isNotBlank(cacheShopJSON)) {
            //存在直接返回
            return JSONUtil.toBean(cacheShopJSON, Shop.class);
        }
        //isBotBlank: 对于空字符串"" 会判断为false，不会直接返回，所以需要我们对CacheShopJSON判断，空字符串并不为null
        //也就是判断命中的是否为空字符串
        if (cacheShopJSON != null) {
            return null;
        }
        //只要不是 空字符串 和 空对象 那么就是不存在店铺信息 就会执行以下步骤
        Shop shop = null;
        try {
            //3.不存在，实现缓存重建
            //3.1 获取互斥锁
            boolean isLock = tryLock("lock:shop:" + id);//每个商店都有自己对应的锁
            //3.2 判断是否获取锁成功
            if (!isLock) {
                //3.3 获取失败，休眠重试等待
                Thread.sleep(100);
                return queryWithMutex(id);//递归
            }
            //3.4 获取缓存成功则根据id查询数据库重建缓存
            //在重建缓存之前应该再次检查是否已经存在缓存
            //redis查询店铺缓存
            String cacheCheck = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
            //判断是否存在
            if (StrUtil.isNotBlank(cacheCheck)) {
                //存在直接返回
                return JSONUtil.toBean(cacheCheck, Shop.class);
            }
            //isBotBlank: 对于空字符串"" 会判断为false，不会直接返回，所以需要我们对CacheShopJSON判断，空字符串并不为null
            //也就是判断命中的是否为空字符串
            if (cacheCheck != null) {
                return null;
            }
            //只要不是 空字符串 和 空对象 那么就是不存在店铺信息 就会执行缓存重建
            shop = getById(id);
            Thread.sleep(200);//模拟重建延迟
            //4.查询不到返回错误 --> 数据库未查询到，那么缓存一个空字符串到redis
            if (shop == null) {
                stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,"",2L, TimeUnit.MINUTES);
                return null;
            }
            //5.查询到了就写入redis并返回
            String shopJSON = JSONUtil.toJsonStr(shop);
            stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,shopJSON,30L, TimeUnit.MINUTES);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            //6.释放锁
            unLock("lock:shop:" + id);
        }
        //返回
        return shop;
    }
```



【基于逻辑过期解决缓存击穿】

![](.\imgs\hmdp-logic.png)

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;//逻辑过期时间
    private Object data;//存放的数据，需要逻辑过期时间
}
```

```java
	/**
     * 根据逻辑过期的重建缓存
     * @param expireTime 逻辑过期时间
     */
    public void saveShop2Redis(Long id, Long expireTime) {
        //1.查询数据库
        Shop shop = getById(id);
        //2.封装逻辑过期时间
        RedisData redisData = new RedisData();
        redisData.setData(shop);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(expireTime));
        //3.写入redis
        stringRedisTemplate.opsForValue().set(RedisConstants.CACHE_SHOP_KEY + id,JSONUtil.toJsonStr(redisData));
    }
```

```java
	private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);//线程池
    public Shop queryWithLogicExpire(Long id) {
        //1.从redis查询店铺缓存
        String cacheShopJSON = stringRedisTemplate.opsForValue().get(RedisConstants.CACHE_SHOP_KEY + id);
        //2.判断是否存在
        if (StrUtil.isBlank(cacheShopJSON)) {
            //不存在直接返回null
            return null;
        }
        //3.存在需要先把JSON反序列化为Java对象
        RedisData redisData = JSONUtil.toBean(cacheShopJSON, RedisData.class);
        Shop shop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);
        LocalDateTime expireTime = redisData.getExpireTime();
        //4.判断缓存是否过期
        if (expireTime.isAfter(LocalDateTime.now())) {
            //4.1 未过期，直接返回店铺信息
            return shop;
        }
        //4.2 过期，则重建缓存
        //5.重建缓存之前需要获取互斥锁
        boolean isLock = tryLock(RedisConstants.LOCK_SHOP_KEY + id);
        //5.1 获取锁成功，开启新线程进行缓存重建
        if (isLock) {
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                try {
                    //缓存重建
                    saveShop2Redis(id,20L);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                } finally {
                    //释放锁
                    unLock(RedisConstants.LOCK_SHOP_KEY + id);
                }
            });
        }
        //6. 获取锁失败或者成功，最终都要返回旧的店铺信息
        return shop;
    }
```

### 2.7 缓存工具封装

【基于StringRedisTemplate封装缓存工具类】 

--借助hutools工具包--

满足以下需求：

* 将任意Java对象序列化为JSON并存储在String类型的key中，并且可以设置TTL过期时间
* 将任意Java对象序列化为JSON并存储在String类型的key中，并且可以设置逻辑过期时间，解决缓存击穿问题
* 根据指定key查询缓存，并反序列化为指定类型，设置缓存空值，解决缓存穿透问题
* 根据指定key查询缓存，并反序列化为指定类型，利用逻辑过期，解决缓存击穿问题

```java
@Data
public class RedisData {
    private LocalDateTime expireTime;//逻辑过期时间
    private Object data;//存放的数据，需要逻辑过期时间
}
```

```java
@Component
@Slf4j
public class CacheUtil {
    private final StringRedisTemplate stringRedisTemplate;

    @Autowired
    public CacheUtil(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    /**
     * 将任意Java对象序列化为JSON并存储在String类型的key中，并且可以设置TTL过期时间
     */
    public void set(String key, Object value, Long time, TimeUnit unit) {
        String valueJSON = JSONUtil.toJsonStr(value);
        stringRedisTemplate.opsForValue().set(key,valueJSON,time,unit);
    }

    /**
     * 将任意Java对象序列化为JSON并存储在String类型的key中，并且可以设置逻辑过期时间，解决缓存击穿问题
     */
    public void setWithLogicExpire(String key, Object value, Long expireTime, TimeUnit unit) {
        RedisData redisData = new RedisData();
        redisData.setData(value);
        redisData.setExpireTime(LocalDateTime.now().plusSeconds(unit.toSeconds(expireTime)));
        stringRedisTemplate.opsForValue().set(key,JSONUtil.toJsonStr(redisData));
    }

    /**
     * 根据指定key查询缓存，并反序列化为指定类型，设置缓存空值，解决缓存穿透问题
     * 利用类型推断，由传入的参数推断出要使用类型
     * @param keyPrefix key的前缀
     * @param id 需要执行操作的类型的id
     * @param RType 要执行操作的类型的字节码文件
     * @param DBFallBack 查询数据库的回调函数
     * @param time TTL
     * @param unit TTL单位
     * @return 要执行操作的类型
     * @param <R> 推断要执行操作的类型
     * @param <IdType> 推断要执行操作的类型的id的类型
     */
    public <R,IdType>/*先定义泛型，再使用泛型*/ R queryWithPassThrough(String keyPrefix, IdType id, Class<R> RType,
                                             Function<IdType,R>/*参数类型，返回类型*/ DBFallBack,
                                             Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        //1.从redis查询R缓存
        String cacheJSON = stringRedisTemplate.opsForValue().get(key);
        //2.判断是否存在
        if (StrUtil.isNotBlank(cacheJSON)) {
            //存在直接返回
            return JSONUtil.toBean(cacheJSON, RType);
        }
        //isBotBlank: 对于空字符串"" 会判断为false，不会直接返回，所以需要我们对CacheJSON判断，空字符串并不为null
        //也就是判断命中的是否为空字符串
        if (cacheJSON != null) {
            return null;
        }
        //只要不是 空字符串 和 空对象 那么就是不存在r信息 就会执行以下步骤
        //3.不存在，根据R's id查询数据库 由调用者编写的回调函数执行查询数据库 参数为idType类型，返回值为R类型
        R r = DBFallBack.apply(id);
        //4.数据库未查询到，那么缓存一个空字符串到redis
        if (r == null) {
            stringRedisTemplate.opsForValue().set(key,"",time, unit);
            return null;
        }
        //5.查询到了就写入redis并返回
        String rJSON = JSONUtil.toJsonStr(r);
        stringRedisTemplate.opsForValue().set(key,rJSON,time, unit);
        return r;
    }

    /**
     * 互斥锁方法
     * @param key 传入的key，作为锁
     * @return 获取锁是否成功
     */
    private boolean tryLock(String key) {
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1", 10, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(flag);//拆箱，避免出现空指针
    }
    /**
     * 释放锁
     * @param key 传入的锁名
     */
    private void unLock(String key) {
        stringRedisTemplate.delete(key);
    }

    private static final ExecutorService CACHE_REBUILD_EXECUTOR = Executors.newFixedThreadPool(10);//线程池
    /**
     * 根据指定key查询缓存，并反序列化为指定类型，利用逻辑过期，解决缓存击穿问题
     * 利用类型推断，由传入的参数推断出要使用类型
     * @param keyPrefix key的前缀
     * @param id 需要执行操作的类型的id
     * @param RType 要执行操作的类型的字节码文件
     * @param DBFallBack 查询数据库的回调函数
     * @param time TTL
     * @param unit TTL单位
     * @return 要执行操作的类型
     * @param <R> 推断要执行操作的类型
     * @param <idType> 推断要执行操作的类型的id的类型
     */
    public <R,idType>/*先定义泛型，再使用泛型*/ R queryWithLogicExpire(String keyPrefix, idType id, Class<R> RType,
                                             Function<idType,R>/*参数类型，返回类型*/ DBFallBack,
                                             Long time, TimeUnit unit) {
        String key = keyPrefix + id;
        //1.从redis查询r缓存
        String cacheJSON = stringRedisTemplate.opsForValue().get(key);
        //2.判断是否存在
        if (StrUtil.isBlank(cacheJSON)) {
            //不存在直接返回null
            return null;
        }
        //3.存在需要先把JSON反序列化为Java对象
        RedisData redisData = JSONUtil.toBean(cacheJSON, RedisData.class);
        R r = JSONUtil.toBean((JSONObject) redisData.getData(), RType);
        LocalDateTime expireTime = redisData.getExpireTime();
        //4.判断缓存是否过期
        if (expireTime.isAfter(LocalDateTime.now())) {
            //4.1 未过期，直接返回r信息
            return r;
        }
        //4.2 过期，则重建缓存
        //5.重建缓存之前需要获取互斥锁
        boolean isLock = tryLock(RedisConstants.LOCK_SHOP_KEY + id);
        //5.1 获取锁成功，开启新线程进行缓存重建
        if (isLock) {
            CACHE_REBUILD_EXECUTOR.submit(() -> {
                try {
                    //缓存重建
                    //1.查询数据库
                    R r1 = DBFallBack.apply(id);
                    //2.封装逻辑过期时间
                    //3.写入redis
                    this.setWithLogicExpire(key,r1,time,unit);
                } catch (Exception e) {
                    throw new RuntimeException(e);
                } finally {
                    //释放锁
                    unLock(RedisConstants.LOCK_SHOP_KEY + id);
                }
            });
        }
        //6. 获取锁失败或者成功，最终都要返回旧的r信息
        return r;
    }
}
```

## 3. 优惠券秒杀

### 3.1 全局唯一ID

在项目中，店铺有【优惠券】，当用户抢购时，就会生成订单并保存到tb_voucher_order这张表中，而订单如果使用数据库自增id就会出现以下问题：

* id规律性太明显
* 受表单数据量的限制



【全局ID生成器】

是一种在分布式系统下用来[生成全局唯一ID]的工具，一般满足以下特性：

![](./imgs/hmdp-12.png)

为了增加ID的安全性，我们可以不直接使用Redis自增的数值，而是拼接一些其他信息：

```tex
	0 - 00000000 00000000 00000000 0000000 - 00000000 00000000 00000000 00000000
 (符号位)				(时间戳 31bit)						(序列号 32bit)
```

![](./imgs/hmdp-13.png)

```java
@Component
public class RedisIDWorker {
    /**
     * 开始时间戳:2023/1/1/00:00:00
     */
    private static final long BEGIN_TIMESTAMP = 1672531200L;

    /**
     * 序列号长度
     */
    private static final int SERIES_NUMBER_BIT = 32;

    private final StringRedisTemplate stringRedisTemplate;

    public RedisIDWorker(StringRedisTemplate stringRedisTemplate) {
        this.stringRedisTemplate = stringRedisTemplate;
    }

    public long nextID(String keyPrefix) {
        //1.生成时间戳: 当前时间戳 - 开始时间戳
        //获取当前时间戳
        LocalDateTime now = LocalDateTime.now();
        long epochSecondForNow = now.toEpochSecond(ZoneOffset.UTC);
        //获取时间戳差
        long epochSecond = epochSecondForNow - BEGIN_TIMESTAMP;
        //2.生成序列号
        //2.1 获取当天日期，精确到天
        String date = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));
        //2.2 自增长
        long increment = stringRedisTemplate.opsForValue().increment("icr:" + keyPrefix + ":" + date);
        //3.拼接并返回
        /*
         * id格式：(符号位 1bit)(时间戳 31bit)(序列号 32bit)
         * 将时间戳最低位左移32位，由0填充
         * 再将位运算后的时间戳和序列号进行【或运算】
         * 因为都是0，而(0,1)和0或运算得到的都是本身(0,1)
         * 换言之，就是序列号和0进行或运算，直接将序列号把32位0填充
         * 达到“拼接效果”
         */
        return epochSecond << SERIES_NUMBER_BIT | increment;
    }

//    public static void main(String[] args) {
//        LocalDateTime time = LocalDateTime.of(2023, 1, 1, 0, 0, 0);
//        long epochSecond = time.toEpochSecond(ZoneOffset.UTC);
//        System.out.println(epochSecond);
//    }
}
```

全局唯一ID生成策略：

* UUID（不实用）
* Redis自增
* 雪花算法
* 数据库自增

Redis自增id策略：

* 每天一个key，方便统计
* ID构造：时间戳 + 计数器（序列号）

### 3.2 实现优惠券秒杀下单

在项目中，每个店铺都可以发放优惠券，分为平价券和特价券。平价券可以任意购买，而特价券需要【秒杀抢购】

表关系如下：

* tb_voucher：优惠券的基本信息，优惠金额，使用规则
* tb_seckill_voucher：优惠券的库存，开始抢购时间，结束抢购时间，特价优惠券才需要填写这些信息



【实现】

下单时需要判断：

* 秒杀是否开始，如果未开始或者已结束则无法下单
* 库存是否充足，不足则无法下单

![](.\imgs\hmdp-14.png)

```java
@Service
public class VoucherOrderServiceImpl extends ServiceImpl<VoucherOrderMapper, VoucherOrder> implements IVoucherOrderService {
    @Resource
    private ISeckillVoucherService seckillVoucherService;
    @Resource
    private RedisIDWorker redisIDWorker;
    @Override
    public Result seckillVoucher(Long voucherId) {
        //1.查询优惠券
        SeckillVoucher seckillVoucher = seckillVoucherService.getById(voucherId);
        //2.判断秒杀是否开始
        if (seckillVoucher.getBeginTime().isAfter(LocalDateTime.now())) {
            return Result.fail("尚未开始!");
        }
        //3.判断秒杀是否结束
        if (seckillVoucher.getEndTime().isBefore(LocalDateTime.now())) {
            return Result.fail("已经结束!");
        }
        //4.判断库存是否充足
        if (seckillVoucher.getStock() < 1) {
            return Result.fail("库存不足!");
        }
        //5.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1")//更新的SQL语句
                .eq("voucher_id", voucherId)//更新的where条件
                .update();
        if (!success) {
            return Result.fail("库存不足!");
        }
        //6.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long id = redisIDWorker.nextID("order");
        voucherOrder.setId(id);
        //用户id
        Long userId = UserHolder.getUser().getId();
        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);
        //7.返回订单id
        return Result.ok(id);
    }
}
```

### 3.3 超卖问题

超卖问题就是典型的[多线程安全问题]，各个线程之间交叉运行，解决方法就是【加锁】：

* 悲观锁：认为线程安全问题一定会发生，添加同步锁，让线程串行执行

  优点：简单粗暴

  缺点：性能一般

* 乐观锁：认为线程安全问题不一定会发生，不加锁，在更新时判断是否有其他线程在修改

  优点：性能好

  缺点：存在成功率的情况，且是直接访问数据库，需要优化

![](.\imgs\hmdp-15.png)



【乐观锁】

乐观锁的关键是判断之前查询到的数据是否被修改过

* 版本号法

  id|stock|version     ===>      id|stock|version

  10|        1|        1 |				 10|       0|        2 |

  ![](.\imgs\hmdp-16.png)

* CAS法（Compare And Swap 比较和交换）（减少了查询的字段，比如直接查询库存是否之前查询到的库存）

  id|stock|		===>		id|stock|

  10|	  1|						10|	   0|

  ![](.\imgs\hmdp-17.png)

成功率低的解决方法：对于库存而言，判断where stock = ？可以改为[where stock > 0]。其他情况具体而定

【将下单功能的第五步修改为CAS的乐观锁】

```java
		//5.扣减库存
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1")//更新的SQL语句: set stock = stock - 1
                .eq("voucher_id", voucherId)
                .gt("stock",0)//更新的where条件: where id = ? and stock > 0
                .update();
        if (!success) {
            return Result.fail("库存不足!");
        }
```

### 3.4 一人一单

【实现一人一单业务】

![](.\imgs\hmdp-19.png)

```java
		//一人一单
        Long user_Id = UserHolder.getUser().getId();
        //查询订单
        int count = query().eq("user_id", user_Id).eq("voucher_id", voucherId).count();
        //判断是否已经存在订单
        if (count > 0) {
            return Result.fail("订单已存在!");
        }
```

依然存在问题，多个线程穿插执行，并发安全问题，解决方案：悲观锁

```java
		//同步锁锁住整个方法，且锁对于同一个用户而言是唯一的，不会妨碍其他用户
        Long userId = UserHolder.getUser().getId();
        synchronized (userId.toString().intern()) {
            //获取代理对象，由代理对象调用带事务的方法，对于Spring的事务@Transactional才可以生效
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            //方法执行完毕，且事务也提交完毕，锁才会释放。避免方法执行完毕之后，锁释放了但事务还没提交，高并发下依然会有线程安全问题
            return proxy.createVoucherOrder(voucherId);
        }

	@Transactional
    public Result createVoucherOrder(Long voucherId) {
        //一人一单
        //用户id
        Long userId = UserHolder.getUser().getId();
        //查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherId).count();
        //判断是否已经存在订单
        if (count > 0) {
            return Result.fail("订单已存在!");
        }
        //6.创建订单
        VoucherOrder voucherOrder = new VoucherOrder();
        //订单id
        long id = redisIDWorker.nextID("order");
        voucherOrder.setId(id);
        //用户id
        voucherOrder.setUserId(userId);
        //代金券id
        voucherOrder.setVoucherId(voucherId);
        save(voucherOrder);
        //7.返回订单id
        return Result.ok(id);
    }
```

此处使用了额外依赖：

```xml
		<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
        </dependency>
```

需要注解开启代理暴露

```java
@SpringBootApplication
@EnableAspectJAutoProxy(exposeProxy = true)//使暴露代理对象
public class HmDianPingApplication {
    public static void main(String[] args) {
        SpringApplication.run(HmDianPingApplication.class, args);
    }
}
```



【一人一单并发安全问题】

通过加锁可以解决在单机情况下的一人一单安全问题，但是在集群的情况下无效

模拟集群环境：

1. 启动两份服务，端口分别为8081和8082

   ![](.\imgs\hmdp-20.png)

2. 修改nginx的conf目录下的nginx.conf文件，配置反向代理和负载均衡，重启nginx服务

   ![](.\imgs\hmdp-21.png)

最终，依然会出现一个用户多个订单的问题

原因是：多个服务【在不同JVM中】，字符串常量池不一样，用户ID自然在不同的环境下是唯一的，高并发依然会导致多单

![](.\imgs\hmdp-22.png)

### 3.5 分布式锁

分布式锁：满足【分布式系统或集群下】【多进程可见】并且【互斥】的锁

![](.\imgs\hmdp-23.png)

![](.\imgs\hmdp-24.png)



【分布式锁的实现】

分布式锁的【核心就是实现多进程之间的互斥】

|        |           MySQL           |         Redis          |            Zookeeper             |
| :----: | :-----------------------: | :--------------------: | :------------------------------: |
|  互斥  | 利用MySQL本身的互斥锁机制 |   利用setnx互斥命令    | 利用节点的唯一性和有序性实现互斥 |
| 高可用 |            好             |           好           |                好                |
| 高性能 |           一般            |           好           |               一般               |
| 安全性 |    断开连接自动释放锁     | 利用锁超时时间到期释放 |    临时节点，断开连接自动释放    |



【基于Redis的分布式锁】

实现分布式锁时需要实现的两个方法：

* 获取锁：

  * 互斥：确保只有一个线程获取锁

  * 非阻塞：尝试一次，成功返回true，失败返回false，不等待

    ```properties
    SET lock thread-1 NX EX 10
    ```

* 释放锁：

  * 手动释放

  * 超时释放：获取锁时设置一个超时时间，避免死锁

    ```properties
    DEL lock
    ```

![](.\imgs\hmdp-25.png)



【Redis分布式锁初版】

```java
/**
 * 分布式锁接口
 */
public interface ILock {
    /**
     * 获取锁
     * @param timeout 超时时间，单位秒
     * @return 获取锁是否成功
     */
    boolean tryLock(long timeout);

    /**
     * 释放锁
     */
    void unlock();
}
```

```java
/**
 * Redis分布式锁 - 初版
 */
public class SimpleRedisLock implements ILock{
    private final StringRedisTemplate stringRedisTemplate;
    private final String LockName;

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String lockName) {
        this.stringRedisTemplate = stringRedisTemplate;
        LockName = lockName;
    }

    @Override
    public boolean tryLock(long timeout) {
        //获取线程标识
        long threadId = Thread.currentThread().getId();
        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent("lock:" + LockName, "" + threadId, timeout, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        stringRedisTemplate.delete("lock:" + LockName);
    }
}
```

```java
/*
	优化一人一单
*/
		//创建锁对象
        Long userId = UserHolder.getUser().getId();//锁住单个用户，只能一人一单
        SimpleRedisLock simpleRedisLock = new SimpleRedisLock(stringRedisTemplate, "order:" + userId);
        //获取锁
        boolean isLock = simpleRedisLock.tryLock(5);
        //判断是否获取锁成功
        if (!isLock) {
            //获取锁失败
            return Result.fail("重复下单!");
        }
        try {
            //获取代理对象，由代理对象调用带事务的方法，对于Spring的事务@Transactional才可以生效
            IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
            //方法执行完毕，且事务也提交完毕，锁才会释放。避免方法执行完毕之后，锁释放了但事务还没提交，高并发下依然会有线程安全问题
            return proxy.createVoucherOrder(voucherId);
        } finally {
            simpleRedisLock.unlock();
        }
```

* 存在问题：当业务出现阻塞，且阻塞时间过长，超过了设置的锁超时时间，使得锁自动被释放，而此时并发下，其余线程获取锁成功，进行业务，之前阻塞的业务突然苏醒完成业务并去释放锁，删除成功，使得其他线程的锁被释放掉

![](.\imgs\hmdp-26.png)

解决方法：将线程id存入锁内，在释放锁时先取出锁内存的线程id对比，看是不是自己对应的线程再释放

![](.\imgs\hmdp-27.png)



【Redis分布式锁改进】

获取锁时存入线程标识（UUID），释放锁时，先判断是否与当前线程标识一致，一致才释放锁

```java
/**
 * Redis分布式锁 - 改进一版
 */
public class SimpleRedisLock implements ILock{
    private final StringRedisTemplate stringRedisTemplate;
    private final String LockName;
    private static final String ID_PREFIX = UUID.randomUUID().toString(true)+"-";//以UUID作为线程标识前缀

    public SimpleRedisLock(StringRedisTemplate stringRedisTemplate, String lockName) {
        this.stringRedisTemplate = stringRedisTemplate;
        LockName = lockName;
    }

    @Override
    public boolean tryLock(long timeout) {
        //获取线程标识
        String threadId = ID_PREFIX + Thread.currentThread().getId();
        //获取锁
        Boolean success = stringRedisTemplate.opsForValue().setIfAbsent("lock:" + LockName, threadId, timeout, TimeUnit.SECONDS);
        return Boolean.TRUE.equals(success);
    }

    @Override
    public void unlock() {
        //获取线程标识
        String threadId = ID_PREFIX + Thread.currentThread().getId();
        //获取锁内的线程标识
        String id = stringRedisTemplate.opsForValue().get("lock:" + LockName);
        //判断是否一致
        if (threadId.equals(id)) {
            //一致就释放锁
            stringRedisTemplate.delete("lock:" + LockName);
        }//不一致就不管
    }
}
```

* 存在问题：【判断锁内的线程标识和自己的线程标识是否一致和释放锁不是[**原子性**]的操作】，当判断成功准备释放锁时出现了阻塞，此时被其他线程获取了锁时，依然会出现释放掉其他线程的锁

  ![](.\imgs\hmdp-28.png)





### 3.6 Redis优化秒杀

### 3.7 Redis消息队列实现异步秒杀

## 4. 达人探店点赞

## 5. 好友关注

## 6. 附近商户

## 7. 用户签到

## 8. UV统计

------

