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

### 3.5 分布式锁(📛重点)

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
	优化黑马点评项目一人一单
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



【确保判断锁内的线程标识和自己的线程标识是否一致和释放锁的**原子性**】

* 可以使用Redis自带的事务配合乐观锁实现，但很复杂，不推荐

* Redis的**Lua脚本**

  Lua，一种编程语言

  可以在一个脚本中编写多条Redis命令，确保多条命令执行时的原子性

  学习参考：[菜鸟网站](https://www.runoob.com/lua/lua-tutorial.html)

Redis提供的**调用函数**，可以**用于执行Redis命令**

```redis
redis.call('命令名称', 'key', '其他参数', ...)
```

```lua
# 在Lua中执行Redis的调用函数
# 例如先执行set name wyh
redis.call('set','name','wyh')
# 再获取name，用lua变量接收
local name = redis.call('get','name')
# 最后返回name
return name
```

Redis执行脚本的命令：

* EVAL script keynums ...

脚本的实质就是字符串，例如执行"redis.call('set','name','wyh')"脚本

```lua
# 调用脚本
eval "redis.call('set','name','wyh')" 0
```

![](.\imgs\hmdp-29.png)

如果脚本中的key，value不想写死，可以作为参数传递。key类型参数会放入**KEYS数组**，其他参数会放入**ARGV数组**，在脚本中可以从这两个数组中获取参数，数组从“1”开始

```lua
eval "redis.call('set',KEYS[1],ARGV[1])" 1 name wyh
```

![](.\imgs\hmdp-30.png)

已知，Redis分布式锁的释放锁的基本流程：

1. 获取锁中的线程标识
2. 判断是否和指定的标识一致
3. 如果一致则释放锁
4. 如果不一致就不管

将以上流程编写为Lua脚本：

```lua
-- 锁的key
local key = "lock:order:5" -- 将来由参数传入KEYS[1]
-- 获取当前线程标识
local threadId = "uuid-xx" -- 将来由参数传入ARGV[1]
-- 获取锁中的线程标识
local id = redis.call('get',key) -- 将来从KEYS[1]获取key
-- 比较线程标识与锁中的标识是否一致
if (id == threadId) then   -- 将来从ARGV[1]获取threadId
    -- 一致就释放锁
    return redis.call('del',key)
end
-- 不一致就返回0
return 0
------------------
------------------
-- 简化为:
-- 比较线程标识与锁中的标识是否一致
if (redis.call('get',KEYS[1]) == ARGV[1]) then
    -- 一致就释放锁
    return redis.call('del',KEYS[1])
end
-- 不一致就返回0
return 0
```



【再次改进Redis分布式锁】

基于Lua脚本实现分布式锁的释放锁逻辑，实现判断标识和释放锁的原子性

RedisTemplate调用Lua脚本的API：

```java
public <T> T execute(
	RedisScript<T> script,
    List<K> keys,
    Object... args
);
```

![](.\imgs\hmdp-31.png)

```java
/**
 * Redis分布式锁
 */
public class SimpleRedisLock implements ILock{
    private final StringRedisTemplate stringRedisTemplate;
    private final String LockName;
    private static final String ID_PREFIX = UUID.randomUUID().toString(true)+"-";//以UUID作为线程标识前缀
    private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;//释放锁的脚本对象
    static {
        UNLOCK_SCRIPT = new DefaultRedisScript<>();//用Redis的默认RedisScript类
        UNLOCK_SCRIPT.setLocation(new ClassPathResource("/script/lua/unlock.lua"));//指定Lua脚本位置：ClassPathResource就是当前项目的resources目录
        UNLOCK_SCRIPT.setResultType(Long.class);//设置脚本返回值类型
    }

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
        stringRedisTemplate.execute(
                UNLOCK_SCRIPT,
                Collections.singletonList("lock:" + LockName),
                ID_PREFIX + Thread.currentThread().getId()
        );
    }
}
```

/script/lua/unlock.lua脚本文件：

```lua
-- 比较线程标识与锁中的标识是否一致
if (redis.call('get',KEYS[1]) == ARGV[1]) then
    -- 一致就释放锁
    return redis.call('del',KEYS[1])
end
-- 不一致就返回0
return 0
```

* 基于Redis的分布式锁的实现思路：
  * 利用set nx ex获取锁，并设置过期时间，保存线程标识
  * 释放锁时先判断线程标识是否与自己线程的一致，一致才del释放锁
* 特性：
  * 利用setnx满足互斥性
  * 利用setex保证故障时锁依然可以释放，避免死锁，提高安全性
  * 利用Redis集群保证高可用和高并发特性



【基于setnx实现的分布式锁存在的问题】

1. 不可重入：同一个线程无法多次获取同一把锁
2. 不可重试：获取锁只尝试一次就返回false，没有重试机制
3. 超时释放：锁超时释放可以解决死锁，但是业务执行耗时较长会导致锁释放，存在误删锁的安全隐患
4. 主从一致性：如果Redis是一个集群环境，主从同步存在延迟，当主机宕机时，如果从并同步主中的锁数据，则会出现锁实现



【**Redisson**】

是一个在Redis基础上实现的Java驻内存数据网格（In-Memory Data Grid）

提供一系列的分布式的Java对象，还提供了许多分布式服务，其中包含了各种分布式锁的实现

```apl
8. 分布式锁（Lock）和同步器（Synchronizer）
	8.1. 可重入锁（Reentrant Lock）
	8.2. 公平锁（Fair Lock）
	8.3. 联锁（MultiLock）
	8.4. 红锁（RedLock）
	8.5. 读写锁（ReadWriteLock）
	8.6. 信号量（Semaphore）
	8.7. 可过期性信号量（PermitExpirableSemaphore）
	8.8. 闭锁（CountDownLatch）
```

[官网](https://redisson.org) | [GitHub地址](https://github.com/redisson/redisson)

Redisson入门

1. 引入依赖

   ```xml
   		<dependency>
               <groupId>org.redisson</groupId>
               <artifactId>redisson</artifactId>
               <version>3.13.6</version>
           </dependency>
   ```

2. 配置Redisson客户端

   ```java
   @Configuration
   public class RedisConfig {
       @Bean
       public RedissonClient redissonClient() {
           //配置类
           Config config = new Config();
           //添加redis地址，此处为单点，使用config.useClusterServers()添加集群地址
           config.useSingleServer().setAddress("redis://192.168.88.92:6379").setPassword("020920");
           //创建客户端
           return Redisson.create(config);
       }
   }
   ```

3. 使用Redisson的分布式锁

   ```java
   @SpringBootTest
   class RedissonApplicationTests {
       @Resource
       private RedissonClient redissonClient;
       @Test
       public void RedissonLockTest() throws InterruptedException {
           //获取（可重入）锁，指定锁的名称
           RLock lock = redissonClient.getLock("anyLock");
           //尝试获取锁，参数：1.获取锁的最大等待时间，期间会重试 2.锁自动释放时间 3.时间单位（无参为失败不重试）
           boolean success = lock.tryLock(1, 10, TimeUnit.SECONDS);
           //判断锁获取成功
           if (success) {
               try {
                   //完成业务
                   System.out.println("执行业务");
               } finally {
                   //业务完成最终释放锁
                   lock.unlock();
               }
           }
       }
   }
   ```



【Redisson可重入锁原理】

锁的重入：就是一个线程获取锁多次

**用Hash结构代替String结构**，替换掉Setnx

HashKey（锁名称）：field（线程标识）：value（获取锁的计数）

整体思想流程：

![](.\imgs\hmdp-32.png)

要将获取锁与释放锁的**逻辑放入Lua脚本，使其保证原子性**

获取锁的Lua脚本：

```Lua
local key = KEY[1]; -- 锁的key
local threadId = ARGV[1]; -- 线程的唯一标识
local releaseTime = ARGV[2]; -- 锁的自动释放时间
-- 判断是否存在
if (redis.call('exists',key) == 0) then
    -- 不存在就获取锁
    redis.call('hset',key,threadId,'1');
    -- 设置超时时间
    redis.call('expire',key,releaseTime);
    return 1; -- 返回结果
end;
-- 锁已经存在，判断threadId是否属于自己
if (redis.call('hexists',key,threadId) == 1) then
    -- 不存在，获取锁
    redis.call('hincrby',key,threadId,'1');
    -- 设置超时时间
    redis.call('expire',key,releaseTime);
    return 1; -- 返回结果
end;
return 0; -- 如果最终走到这，说明锁不属于该线程，获取锁失败
```

释放锁的Lua脚本：

```lua
local key = KEY[1]; -- 锁的key
local threadId = ARGV[1]; -- 线程的唯一标识
local releaseTime = ARGV[2]; -- 锁的自动释放时间
-- 判断当前锁是否属于自己
if (redis.call('hexists',key，threadId) == 0) then
    return nil; -- 不是自己的，直接返回失败结果，不做后续动作
end;
-- 是自己的锁，则重入次数减一
local count = redis.call('hincrby',key,threadId,-1)
-- 判断重入次数是否已经为0 -- 即为最外层业务行为
if (count > 0) then
    -- 大于0，说明还不能释放删除锁，还不是最外层业务，重置有效期
    redis.call('expire',key,releaseTime);
    return nil; -- 直接返回
else
    -- 为0说明可以释放锁了，直接删除即可
    redis.call('del',key);
    return nil;
end;
```



【Redisson可重入分布式锁原理】

![](.\imgs\hmdp-33.png)

* 可重入：利用hash结构记录线程id和重入次数
* 可重试：利用信号量和发布订阅（PubSub）功能实现等待，唤醒，获取锁失败的重试机制
* 超时更新续约：利用WatchDog定时任务，每隔一段时间（releaseTime/3），重置超时时间



【Redisson分布式锁主从一致性问题】

集群模式下：

![](.\imgs\hmdp-34.png)

**Redisson的MultiLock**：

![](.\imgs\hmdp-36.png)

各个redis的node节点没有关联，但是保存的数据都是一致的，获取锁时需要在所有的node中获取成功才算获取锁成功，不然就算失败，解决主从一致性问题



【Redis分布式锁小结】

* **不可重入Redis分布式锁**：
  * 原理：利用setnx的互斥性，利用ex防止死锁，释放锁时判断线程标识
  * 缺陷：不可重入，无法重试，锁超时失效
* **可重入Redis分布式锁（Redisson）**
  * 原理：利用hash结构，记录线程标识和重入次数，利用watchDog延续锁时间，利用信号量控制锁重试等待
  * 缺陷：redis宕机引起锁失效
* **Redisson的multiLock**：
  * 原理：多个独立的Redis节点，必须在所有的节点中都获取到了锁才算获取锁成功
  * 缺陷：运维成本太高，实现复杂

### 3.6 Redis优化秒杀

将一条龙服务给分离，使得性能提高

![](.\imgs\hmdp-37.png)

用Lua脚本确保判断用户是否具有秒杀资格的原子性

更具是否有资格，异步开启新线程执行下单操作



【改进秒杀业务，提高并发能力】

* 新增秒杀优惠券的同时，将优惠券信息保存到Redis中
* 基于Lua脚本，判断秒杀库存，一人一单，决定用户是否秒杀成功
* 如果抢购成功，将优惠券id和用户id封装到阻塞队列
* 开启线程任务，不断从阻塞队列中获取信息，实现异步下单

```java
	@Override
    public Result seckillVoucher(Long voucherId) {
        //1.执行Lua脚本
        Long result = stringRedisTemplate.execute(
                SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(),
                UserHolder.getUser().getId().toString()
        );
        //2.判断执行结果
        if (result.intValue() != 0) {
            //3.不为0，没有购买资格
            return Result.fail(result.intValue() == 1 ? "库存不足!" : "不能重复下单!");
        }
        //4.为0，有购买资格，将下单信息保存到阻塞队列
        VoucherOrder voucherOrder = new VoucherOrder();
        long orderId = redisIDWorker.nextID("order");
        voucherOrder.setVoucherId(voucherId);
        voucherOrder.setUserId(UserHolder.getUser().getId());
        voucherOrder.setId(orderId);
        orderTasks.add(voucherOrder);
        //获取代理对象，由代理对象调用带事务的方法，对于Spring的事务@Transactional才可以生效
        proxy = (IVoucherOrderService) AopContext.currentProxy();
        //5.返回订单id
        return Result.ok(orderId);
    }
```

```java
	@Resource
    private StringRedisTemplate stringRedisTemplate;
    private static final DefaultRedisScript<Long> SECKILL_SCRIPT;
    static {
        SECKILL_SCRIPT = new DefaultRedisScript<>();
        SECKILL_SCRIPT.setLocation(new ClassPathResource("/script/lua/seckill.lua"));
        SECKILL_SCRIPT.setResultType(Long.class);
    }
    /**
     * 阻塞队列
     * 当线程尝试在阻塞队列获取元素时，如果队列元素为空，那么会被阻塞等待
     */
    private static final BlockingQueue<VoucherOrder> orderTasks = new ArrayBlockingQueue<>(1024 * 1024);
    private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
    @PostConstruct//在类初始化时执行以下方法
    private void init() {
        SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
    }
    private class VoucherOrderHandler implements Runnable {
        @Override
        public void run() {
            while (true) {
                try {
                    //获取队列中的订单信息
                    VoucherOrder voucherOrder = orderTasks.take();
                    //创建订单
                    handleVoucherOrder(voucherOrder);
                } catch (Exception e) {
                    log.error("订单处理异常",e);
                }
            }
        }
    }
    private IVoucherOrderService proxy;
    private void handleVoucherOrder(VoucherOrder voucherOrder) {
        //创建锁对象
        Long userId = UserHolder.getUser().getId();//锁住单个用户，只能一人一单
        SimpleRedisLock simpleRedisLock = new SimpleRedisLock(stringRedisTemplate, "order:" + userId);
        //获取锁
        boolean isLock = simpleRedisLock.tryLock(5);
        //判断是否获取锁成功
        if (!isLock) {
            //获取锁失败
            log.error("重复下单!");
            return;
        }
        try {
            //方法执行完毕，且事务也提交完毕，锁才会释放。避免方法执行完毕之后，锁释放了但事务还没提交，高并发下依然会有线程安全问题
            proxy.createVoucherOrder(voucherOrder);
        } finally {
            simpleRedisLock.unlock();
        }
    }
```

```java
	@Transactional
    public void createVoucherOrder(VoucherOrder voucherOrder) {
        //一人一单
        //用户id
        Long userId = voucherOrder.getUserId();
        //查询订单
        int count = query().eq("user_id", userId).eq("voucher_id", voucherOrder.getVoucherId()).count();
        //判断是否已经存在订单
        if (count > 0) {
            log.error("订单已存在!");
            return;
        }
        boolean success = seckillVoucherService.update()
                .setSql("stock = stock - 1")
                .eq("voucher_id", voucherOrder.getVoucherId()).gt("stock", 0)
                .update();
        if (!success) {
            log.error("库存不足!");
            return;
        }
        save(voucherOrder);
    }
```

秒杀业务的思路：

* 先利用Redis完成库存余量，一人一单判断，完成抢单业务
* 再将下单业务放入阻塞队列。利用独立线程异步下单

基于阻塞队列的异步秒杀存在的问题：

* 内存限制问题（JDK提供的阻塞队列）
* 数据安全问题（服务宕机）

### 3.7 Redis消息队列实现异步秒杀

使用消息队列代替阻塞队列

不受JVM内存的限制

我们可以选择：RabbitMQ RocketMQ等等MQ产品

但是，**Redis也可以实现消息队列功能**：

* list结构：基于List结构模拟消息队列
* Pubsub：基本的点对点消息模型（Redis  v2.0）
* Stream：比较完善的消息队列模型（Redis  v5.0）



【基于List结构模拟消息队列】

redis的list数据结构是一个双向链表，模拟入列出列效果（lpush，rpop）

要实现阻塞的效果，需要使用lpush，brpop

优缺点：

* 优点：
  * 利用Redis存储。不受限于JVM内存上限
  * 基于Redis的持久化机制，数据安全性有保证
  * 可以满足消息有序性
* 缺点：
  * 无法避免消息的丢失
  * 只支持单消费者



【基于PubSub的消息队列】

PubSub是redis2.0引入的消息传递模型

消费者可以订阅一个或多个channel

* subscribe channel [channel]：订阅一个或多个频道
* publish channel msg：向一个频道发送消息
* psubscribe pattern [pattern]：订阅与pattern格式匹配的所有频道

优缺点：

* 优点：
  * 采取发布订阅模型。支持多生产多消费
* 缺点：
  * 不支持数据持久化
  * 无法避免消息丢失
  * 消息堆积有上限，超出时数据会丢失



【基于stream的消息队列 - XREAD】

stream是redis5.0引入的一种**新的数据类型**，可以实现一个功能非常完善的消息队列

发送消息命令：

![](.\imgs\hmdp-38.png)

```shell
#创建名为users的消息队列，并向其中发送一个消息，内容为：{name=jack，age=21}，并使用Redis自动生成的id
xadd users * name jack age 21
```

读取消息的命令：

![](.\imgs\hmdp-39.png)

```shell
#读取第一个消息
xread count 1 streams users 0
#阻塞方式，读取最新消息
xread count 1 block 1000 streams users $
```

* 当我们指定起始id为$时，代表读取最新的消息，如果在处理一条消息的过程中，又有超过一条的消息送入了消息队列，下次读取时只会读取最新的那条消息，就会造成消息漏读

stream的xread命令优缺点：

* 优点：
  * 消息可以回溯
  * 一个消息可以被多个消费者读取
  * 可以阻塞读取
* 缺点：
  * 会消息漏读



【基于stream的消息队列 - 消费者组】

消费者组（Consumer Group）：将多个消费者划分到同一个组中，监听同一个队列，具有如下特点：

![](.\imgs\hmdp-40.png)

创建消费者组：

![](.\imgs\hmdp-41.png)

其他命令：

![](.\imgs\hmdp-42.png)

从消费者组读取消息：

![](.\imgs\hmdp-43.png)

stream的xreadgroup命令优缺点：

* 优点：
  * 消息可以回溯
  * 可以多消费者争抢消息，加快消费速度
  * 可以阻塞读取
  * 没有消息漏读的风险
  * 有消息确认机制，保证消息至少被消费一次



【Redis消息队列总结】

|              |                   List                   |       PubSub       |                         Stream                         |
| :----------: | :--------------------------------------: | :----------------: | :----------------------------------------------------: |
|  消息持久化  |                   支持                   |       不支持       |                          支持                          |
|   阻塞读取   |                   支持                   |        支持        |                          支持                          |
| 消息堆积处理 | 受限于内存空间，可以利用多消费者加快处理 | 受限于消费者缓冲区 | 受限于队列长度，可以利用消费者组提高消费速度，减少堆积 |
| 消息确认机制 |                  不支持                  |       不支持       |                          支持                          |
|   消息回溯   |                  不支持                  |       不支持       |                          支持                          |

* Stream > List > PubSub



【基于Redis的Stream结构作为消息队列，实现异步秒杀下单】

* 创建一个Stream类型的消息队列，名为stream.orders
* 修改之前的秒杀下单Lua脚本，在认定有抢购资格之后，直接向stream.orders添加信息，内容包含voucherId，userId，orderId
* 项目启动时，开启一个线程任务，尝试获取stream.orders中的消息，完成下单

```shell
xgroup create stream.orders g1 0 mkstream
```

```lua
-- 1.参数列表:
-- 优惠券id
local voucherId = ARGV[1]
-- 用户id
local userId = ARGV[2]
-- 订单id
local orderId = ARGV[3]

-- 2.数据key
-- 库存key
local stockKey = 'seckill:stock:' .. voucherId
-- 订单key
local orderKey = 'seckill:order:' .. voucherId

-- 3.脚本业务
-- 判断库存是否充足 get stockKey
if (tonumber(redis.call('get'.stockKey) <= 0)) then
    -- 库存不足，返回1
    return 1
end
-- 再判断用户是否下单 sismember orderKey userId
if (redis.call('sismember', orderKey, userId) == 1) then
    -- 存在，返回2
    return 2
end
-- 走到这一步，即表示用户拥有了抢购资格，扣减库存 incrby stockKey -1
redis.call('incrby',stockKey,-1)
-- 下单，保存用户 sadd orderKey userId
redis.call('sadd',orderKey,userId)
-- 发送消息到消息队列 xadd stream.orders * k1 v1 k2 v2 ...
redis.call("xadd",'stream.orders','*','userId',userId,'voucherId',voucherId,'id',orderId)
return 0
```

```java
	private IVoucherOrderService proxy;	
	@Override
    public Result seckillVoucher(Long voucherId) {
        long orderId = redisIDWorker.nextID("order");
        String userId = UserHolder.getUser().getId().toString();
        //1.执行Lua脚本
        Long result = stringRedisTemplate.execute(
                SECKILL_SCRIPT,
                Collections.emptyList(),
                voucherId.toString(),
                userId,
                String.valueOf(orderId)
        );
        //2.判断执行结果
        if (result.intValue() != 0) {
            //3.不为0，没有购买资格
            return Result.fail(result.intValue() == 1 ? "库存不足!" : "不能重复下单!");
        }
        //获取代理对象，由代理对象调用带事务的方法，对于Spring的事务@Transactional才可以生效
        proxy = (IVoucherOrderService) AopContext.currentProxy();
        //3.返回订单id
        return Result.ok(orderId);
    }

	private static final ExecutorService SECKILL_ORDER_EXECUTOR = Executors.newSingleThreadExecutor();
    @PostConstruct//在类初始化时执行以下方法
    private void init() {
        SECKILL_ORDER_EXECUTOR.submit(new VoucherOrderHandler());
    }
    private class VoucherOrderHandler implements Runnable {
        private final String queueName = "stream.orders";
        @Override
        public void run() {
            while (true) {
                try {
                    //获取消息队列中的订单信息 xreadgroup group g1 c1 count 1 block 2000 streams stream.orders >
                    List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                            Consumer.from("g1", "c1"),
                            StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                            StreamOffset.create(queueName, ReadOffset.lastConsumed())
                    );
                    //判断消息获取是否成功
                    if (list == null || list.isEmpty()) {
                        //获取失败，说明没有消息，继续下一次循环
                        continue;
                    }
                    //获取成功，下单
                    MapRecord<String, Object, Object> record = list.get(0);
                    Map<Object, Object> value = record.getValue();
                    VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                    handleVoucherOrder(voucherOrder);
                    //ACK确认收到消息 sack stream.orders g1 msgId
                    stringRedisTemplate.opsForStream().acknowledge(queueName,"g1",record.getId());
                } catch (Exception e) {
                    log.error("订单处理异常",e);
                    handlePendingList();
                }
            }
        }

        private void handlePendingList() {
            while (true) {
                try {
                    //获取pending list中的订单信息 xreadgroup group g1 c1 count 1 block 2000 streams stream.orders 0
                    List<MapRecord<String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                            Consumer.from("g1", "c1"),
                            StreamReadOptions.empty().count(1),
                            StreamOffset.create(queueName, ReadOffset.from("0"))
                    );
                    //判断消息获取是否成功
                    if (list == null || list.isEmpty()) {
                        //获取失败，说明没有消息，结束循环
                        break;
                    }
                    //获取成功，下单
                    MapRecord<String, Object, Object> record = list.get(0);
                    Map<Object, Object> value = record.getValue();
                    VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(value, new VoucherOrder(), true);
                    handleVoucherOrder(voucherOrder);
                    //ACK确认收到消息 sack stream.orders g1 msgId
                    stringRedisTemplate.opsForStream().acknowledge(queueName, "g1", record.getId());
                } catch (Exception e) {
                    log.error("处理Pending-List异常",e);
                    try {
                        Thread.sleep(20);
                    } catch (InterruptedException ex) {
                        ex.printStackTrace();
                    }
                }
            }
        }
    }
```

## 4. 达人探店点赞

### 4.1 发布探店笔记

探店笔记类似于网站的评价，往往是图文结合，对应的表：

* tb_blog：探店笔记表，包含笔记中的标题，文字，图片
* tb_blog_comments：其他用户对探店笔记的评价

修改资料中的黑马点评项目前端服务器保存图片地址为对应存放地址



【实现查看探店笔记】

```java
	private void queryBlogUser(Blog blog) {
        Long userId = blog.getUserId();
        User user = userService.getById(userId);
        blog.setName(user.getNickName());
        blog.setIcon(user.getIcon());
    }

    @Override
    public Result queryBlogById(Long id) {
        //查询blog
        Blog blog = getById(id);
        if (blog == null) {
            return Result.fail("BLOG不存在!");
        }
        //查询blog有关的用户
        queryBlogUser(blog);
        return Result.ok(blog);
    }
```

### 4.2 点赞

【完善点赞功能】

* 同一个用户只能点赞一次，再次点击则取消点赞
* 如果当前用户已经点赞，则点赞按钮高亮显示（由前端实现，判断Blog类的isLike属性）

实现步骤：

1. 给Blog类添加isLike字段，表示是否被当前用户点赞
2. 修改点赞功能，利用Redis的Set集合判断是否被点赞过，未点赞则点赞数+1，已点过赞则点赞数-1
3. 修改根据id查询Blog业务。判断当前登录的用户是否点过赞，赋值给isLike
4. 修改分页查询Blog业务，判断当前登录的用户是否点过赞，赋值给isLike

```java
	private void isBlogLiked(Blog blog) {
        //获取登录用户
        Long userId = UserHolder.getUser().getId();
        //判断当前登录用户是否已经点赞
        String key = RedisConstants.BLOG_LIKED_KEY + blog.getId();
        Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
        blog.setIsLike(BooleanUtil.isTrue(isMember));
    }

    @Override
    public Result likeBlog(Long id) {
        //获取登录用户
        Long userId = UserHolder.getUser().getId();
        //判断当前登录用户是否已经点赞
        String key = RedisConstants.BLOG_LIKED_KEY + id;
        Boolean isMember = stringRedisTemplate.opsForSet().isMember(key, userId.toString());
        //如果未点赞，则可以点赞
        if (BooleanUtil.isFalse(isMember)) {
            //点赞成功，数据库点赞+1
            boolean update = update().setSql("liked = liked + 1").eq("id", id).update();
            //保存用户到Redis的set集合
            if (update) {
                stringRedisTemplate.opsForSet().add(key,userId.toString());
            }
        }
        //如果已点赞，则不可以点赞
        //点赞取消，数据库点赞-1
        boolean update = update().setSql("liked = liked - 1").eq("id", id).update();
        //从Redis的set集合移除用户
        stringRedisTemplate.opsForSet().remove(key,userId.toString());
        return Result.ok();
    }
```

### 4.3 点赞排行榜

在探店笔记的详情界面，会把笔记点赞的人显示出来，如最早的点赞TOP5，形成点赞排行榜

 【实现查询点赞排行榜接口】

使用Redis的SortedSet代替Set

```java
	@Override
    public Result queryBlogLikes(Long id) {
        //查询top5的点赞用户 zrange key 0 4
        String key = RedisConstants.BLOG_LIKED_KEY + id;
        Set<String> top5 = stringRedisTemplate.opsForZSet().range(key, 0, 4);
        if (top5 == null || top5.isEmpty()) {
            return Result.ok(Collections.emptyList());
        }
        //解析其中的用户id
        List<Long> ids = top5.stream().map(Long::valueOf).collect(Collectors.toList());
        //根据id查询用户: where id in (5,1) order by field(id,5,1)，必须加上order by field，不然数据库查询出来的不是5,1顺序
        String idStr = StrUtil.join(",", ids);//用逗号拼接字符串为 5,1,2,...
        List<UserDTO> userDTOS = userService.query().in("id",ids).last("order by field(id,"+idStr+")").list()
                .stream()
                .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
                .collect(Collectors.toList());
        //返回
        return Result.ok(userDTOS);
    }
```

## 5. 好友关注

### 5.1 关注和取关

【实现关注和取关】

1. 关注和取关接口
2. 判断是否关注接口
3. 需要把tb_follow表的主键改为自增长，简化开发

```java
@Service
public class FollowServiceImpl extends ServiceImpl<FollowMapper, Follow> implements IFollowService {

    @Override
    public Result follow(Long followUserId, Boolean isFollow) {
        //获取当前登录用户
        UserDTO user = UserHolder.getUser();
        Long userId = user.getId();
        //判断关注还是取关
        if (isFollow) {
            //关注就新增数据
            Follow follow = new Follow();
            follow.setUserId(userId);
            follow.setFollowUserId(followUserId);
            save(follow);
        } else {
            //取关就删除数据
            remove(new QueryWrapper<Follow>()
                    .eq("user_id",userId)
                    .eq("follow_user_id",followUserId)
            );
        }
        return Result.ok();
    }

    @Override
    public Result isFollow(Long followUserId) {
        //获取登录用户id
        Long userId = UserHolder.getUser().getId();
        //查询是否关注
        Integer count = query()
                .eq("user_id", userId)
                .eq("follow_user_id", followUserId)
                .count();
        //判断
        return Result.ok(count > 0);
    }
}
```

### 5.2 共同关注

点击博主头像，进入博主首页，会查询该用户，以及该用户的BLOG，这两个功能和当前功能，Redis无关，所以直接使用资料提供的“代码片段”CV即可

在博主首页，会显示与该博主的共同关注

【实现共同关注】

利用Redis的Set数据结构，利用其“交集‘功能，查询出共同关注

修改上面的关注功能，添加关注时存入redis

```java
@Service
public class FollowServiceImpl extends ServiceImpl<FollowMapper, Follow> implements IFollowService {
    @Resource
    private StringRedisTemplate stringRedisTemplate;
    @Resource
    private IUserService userService;

    @Override
    public Result follow(Long followUserId, Boolean isFollow) {
        //获取当前登录用户
        UserDTO user = UserHolder.getUser();
        Long userId = user.getId();
        //判断关注还是取关
        if (isFollow) {
            //关注就新增数据
            Follow follow = new Follow();
            follow.setUserId(userId);
            follow.setFollowUserId(followUserId);
            boolean isSuccess = save(follow);
            if (isSuccess) {
                //把关注的用户放入Redis的Set集合，方便后续进行交集操作
                stringRedisTemplate.opsForSet().add("follows:"+userId,followUserId.toString());
            }
        } else {
            //取关就删除数据
            boolean isSuccess = remove(new QueryWrapper<Follow>()
                    .eq("user_id", userId)
                    .eq("follow_user_id", followUserId)
            );
            if (isSuccess) {
                //同时在Redis中移除
                stringRedisTemplate.opsForSet().remove("follows:"+userId,followUserId.toString());
            }
        }
        return Result.ok();
    }

    @Override
    public Result isFollow(Long followUserId) {
        //获取登录用户id
        Long userId = UserHolder.getUser().getId();
        //查询是否关注
        Integer count = query()
                .eq("user_id", userId)
                .eq("follow_user_id", followUserId)
                .count();
        //判断
        return Result.ok(count > 0);
    }

    @Override
    public Result followCommons(Long id) {
        //获取当前登录用户
        Long userId = UserHolder.getUser().getId();
        //求交集
        Set<String> intersect = stringRedisTemplate.opsForSet().intersect("follows:" + userId, "follows:" + id);
        if (intersect == null || intersect.isEmpty()) {
            //无交集
            return Result.ok(Collections.emptyList());
        }
        //解析交集id
        List<Long> userIdList = intersect.stream().map(Long::valueOf).collect(Collectors.toList());
        //查询交集用户
        List<UserDTO> userDTOS = userService.listByIds(userIdList)
                .stream()
                .map(user -> BeanUtil.copyProperties(user, UserDTO.class))
                .collect(Collectors.toList());
        return Result.ok(userDTOS);
    }
}
```

### 5.3 关注推送

关注推送，也叫Feed流，直译为“投喂”。为用户持续的提供“沉浸式”的体验，通过无限下拉刷新获取新的信息

Feed流有两种常见模式：

* TimeLine：不做内容筛选，简单的按照内容发布时间排序，常用于好友或关注，例如朋友圈
  * 优点：信息全面，实现简单
  * 缺点：信息杂糅，用户不一定感兴趣，内容获取率低
* 智能排序：利用只能算法屏蔽掉违规的，用户不感兴趣的内容，推送用户感兴趣的内容吸引用户
  * 优点：投喂用户感兴趣内容，用户黏度较高，容易沉迷
  * 缺点：实现复杂，算法不精准会有反作用

此处的关注推送是基于关注列表实现，所以采用TimeLine模式

TimeLine模式有三种实现方案：

* 拉模式

  * 也叫读扩散

  ![](.\imgs\hmdp-44.png)

  * 缺点：延迟

* 推模式

  * 也叫写扩散，低延迟

  ![](.\imgs\hmdp-45.png)

  * 缺点：内存占用高

* 推拉结合

  * 也叫读写混合，兼具推和拉两种模式的优点

  ![](.\imgs\hmdp-46.png)

|              |  拉模式  |            推模式             |       推拉结合        |
| :----------: | :------: | :---------------------------: | :-------------------: |
|    写比例    |    低    |              高               |          中           |
|    读比例    |    高    |              低               |          中           |
| 用户读取延迟 |    高    |              低               |          低           |
|   实现难度   |   复杂   |             简单              |        很复杂         |
|   使用场景   | 很少使用 | 用户量少（少于千万），没有大V | 过千万用户的，且有大V |



【基于推模式实现关注推送】

1. 修改新增探店笔记业务。在保存BLOG到数据库的同时。推送到粉丝的“收件箱”
2. 收件箱满足可以根据时间戳排序，必须用Redis的数据结构实现：SortedSet
3. 查询收件箱时，可以实现分页查询

在Feed流中，数据会不断更新，数据的角标在不断变化，因此不能采用传统的分页模式，会重复读取

所以我们采用滚动分页，使用Redis的SortedSet实现，记录时间戳排序位置

![](.\imgs\hmdp-47.png)

推送：

```java
	@Override
    public Result saveBlog(Blog blog) {
        // 获取登录用户
        UserDTO user = UserHolder.getUser();
        blog.setUserId(user.getId());
        // 保存探店博文
        boolean isSuccess = save(blog);
        if (isSuccess) {
            return Result.fail("新增笔记失败!");
        }
        //查询笔记作者的所有粉丝
        List<Follow> follows = followService.query().eq("follow_user_id", user.getId()).list();
        //推送笔记id给所有粉丝
        for (Follow follow : follows) {
            //获取粉丝id
            Long userId = follow.getUserId();
            //推送BLOGid给粉丝收件箱
            stringRedisTemplate.opsForZSet().add("feed:"+userId,blog.getId().toString(),System.currentTimeMillis());
        }
        // 返回id
        return Result.ok(blog.getId());
    }
```



【实现关注推送的分页查询】

在个人主页的关注一栏，查询并展示推送的BLOG

利用Redis的SortedSet的范围查询带分数

滚动分页查询：

* max：当前时间戳 | 上一次查询的最小时间戳
* min：0
* offset：0 | 上一次查询中，与最小值一样的元素个数
* count：3

接口返回值：

* List\<Blog>：小于指定时间戳的笔记集合
* minTime：本次查询的推送最小时间戳
* offset：偏移量

```java
@Data
public class ScrollResult {
    private List<?> list;
    private Long minTime;
    private Integer offset;
}
```

```java
	@Override
    public Result queryBlogOfFollow(Long max, Integer offset) {
        //获取当前用户
        Long userId = UserHolder.getUser().getId();
        //查询当前用户收件箱
        Set<ZSetOperations.TypedTuple<String>> typedTuples = stringRedisTemplate.opsForZSet().reverseRangeByScoreWithScores(
                "feed:" + userId,
                0,
                max,
                offset,
                3
        );
        if (typedTuples == null || typedTuples.isEmpty()) {
            return Result.ok(Collections.emptyList());
        }
        //解析收件箱数据：BlogID minTime offset
        List<Long> ids = new ArrayList<>(typedTuples.size());
        long minTime = 0;
        int OffSet = 1;
        for (ZSetOperations.TypedTuple<String> typedTuple : typedTuples) {
            //获取id
            ids.add(Long.valueOf(typedTuple.getValue()));
            //获取时间戳（分数）
            long time = typedTuple.getScore().longValue();
            if (time == minTime) {
                OffSet++;
            } else {
                minTime = time;
                OffSet = 1;
            }
        }
        //查询Blog
        String idStr = StrUtil.join(",", ids);//用逗号拼接字符串为 5,1,2,...
        List<Blog> blogs = query().in("id", ids).last("order by field(id," + idStr + ")").list();
        for (Blog blog : blogs) {
            //查询blog有关的用户
            queryBlogUser(blog);
            //查询blog是否被当前用户点赞点赞
            isBlogLiked(blog);
        }
        //封装并返回
        ScrollResult scrollResult = new ScrollResult();
        scrollResult.setList(blogs);
        scrollResult.setOffset(OffSet);
        scrollResult.setMinTime(minTime);
        return Result.ok(scrollResult);
    }
```

## 6. 附近商户

### 6.1 GEO数据结构

GEO为Geolocation的简写，代表地理坐标。Redis3.2版本加入的，允许存储地理坐标，帮助我们根据经纬度检索数据

常用命令：

* GEOADD：添加了一个地理空间坐标，包含：经度（longitude）维度（latitude）值（member）
* GEODIST：计算指定两个点之间的距离并返回
* GEOHASH：将指定member的坐标转为hash字符串形式并返回
* GEOPOS：返回指定member的坐标
* GEORADIUS：指定圆心，半径，找到该圆内包含的所有member，并按照与圆心之间的距离排序并返回（Redis6.2之后弃用）
* GEOSEARCH：在指定范围内搜索member，并按照与指定点之间的距离排序后返回。范围可以是矩形或圆形（Redis6.2新功能）
* GEOEARCHSTORE：与GEOSEARCH功能一致，多了个将结果存储到一个指定key中的功能（Redis6.2新功能）

```shell
# redis GEO结构演示：
GEOADD g1 经度 维度 m1 经度 维度 m2 经度 维度 m3
GEODIST g1 m1 m2
```

### 6.2 附近商户搜索

在首页中点击某个频道，可以看见频道下的商户

按照商户类型做分组，类型相同的商户作为同一组，以typeID作为Key存入同一个GEO集合中

导入数据到redis存入GEO类型：

```java
	@Test
    public void loadShopData() {
        //查询店铺信息
        List<Shop> list = service.list();
        //把店铺分组，按照typeID分组，id一致放入一个集合
        Map<Long, List<Shop>> map = list.stream().collect(Collectors.groupingBy(Shop::getTypeId));
        //分批完成写入Redis
        for (Map.Entry<Long, List<Shop>> entry : map.entrySet()) {
            //获取类型id
            Long typeId = entry.getKey();
            //获取同类型店铺的集合
            List<Shop> value = entry.getValue();
            List<RedisGeoCommands.GeoLocation<String>> locations = new ArrayList<>(value.size());
            //写入Redis GEOADD key 经度 维度 member
            for (Shop shop : value) {
                locations.add(new RedisGeoCommands.GeoLocation<>(
                        shop.getId().toString(),
                        new Point(shop.getX(),shop.getY())
                ));
            }
            stringRedisTemplate.opsForGeo().add("shop:geo:"+typeId,locations);
        }
    }
```

SpringDataRedis的2.3.9版本不支持Redis的6.2提供的GEOSEARCH命令，修改POM的SpringDataRedis版本，高于2.6即可

```java
@Override
    public Result queryShopByType(Integer typeId, Integer current, Double x, Double y) {
        //判断是否需要根据坐标查询
        if (x == null || y == null) {
            // 根据类型分页查询
            Page<Shop> page = query()
                    .eq("type_id", typeId)
                    .page(new Page<>(current, SystemConstants.DEFAULT_PAGE_SIZE));
            // 返回数据
            return Result.ok(page.getRecords());
        }
        //计算分页参数
        int begin = (current - 1) * SystemConstants.DEFAULT_PAGE_SIZE;
        int end = current * SystemConstants.DEFAULT_PAGE_SIZE;
        //查询redis，按照距离排序，分页
        GeoResults<RedisGeoCommands.GeoLocation<String>> results = stringRedisTemplate.opsForGeo().search(
                "shop:geo:" + typeId,
                GeoReference.fromCoordinate(x, y),
                new Distance(5000),//5000m范围内
                RedisGeoCommands.GeoSearchCommandArgs.newGeoSearchArgs().includeDistance().limit(end)
        );
        //解析出id
        if (results == null) {
            return Result.ok(Collections.emptyList());
        }
        List<GeoResult<RedisGeoCommands.GeoLocation<String>>> list = results.getContent();
        if (list.size() <= begin) {
            return Result.ok("没有更多数据了~");
        }
        //截取begin - end的部分
        List<Long> ids = new ArrayList<>(list.size());
        Map<String,Distance> distanceMap = new HashMap<>(list.size());
        list.stream().skip(begin).forEach(result -> {
            //获取店铺id
            String shopIdStr = result.getContent().getName();
            ids.add(Long.valueOf(shopIdStr));
            //获取距离
            Distance distance = result.getDistance();
            distanceMap.put(shopIdStr,distance);
        });
        //根据id查询shop
        String idStr = StrUtil.join(",", ids);//用逗号拼接字符串为 5,1,2,...
        List<Shop> shops = query().in("id", ids).last("order by field(id," + idStr + ")").list();
        for (Shop shop : shops) {
            shop.setDistance(distanceMap.get(shop.getId().toString()).getValue());
        }
        //返回
        return Result.ok(shops);
    }
```

## 7. 用户签到

### 7.1 BitMap用法

对于签到统计，我们按月来统计用户签到信息，签到记录为1，未签到记录为0

那么一个月的签到记录最多只占31bit

而把每一个bit对应当月的每一天，形成了映射关系。用0和1表示业务状态，这种思路称为位图（BitMap）

**Redis中利用String类型数据结构实现BitMap**，因此最大上限为512M，转为bit为2^32bit

BitMap常用命令：

* SETBIT：向指定位置（offset）存入0或1
* GETBIT：获取指定位置（offset）的值
* BITCOUNT：统计BitMap中值为1的bit个数
* BITFIELD：操作（查询，修改，自增）BitMap中bit数组中指定位置（offset）的值（一般用来作查询）
* BITFIELD_RO：获取BitMap中bit数组。并以十进制形式返回
* BITOP：将多个BitMap的结果做位运算（与，或，异或）
* BITPOS：查找Bit数组中指定范围内第一个出现0或1的位置

由于BitMap底层由String数据结构实现，所以操作被封装到了字符串相关操作中了（opsForValue）

### 7.2 签到功能

【实现签到功能】

实现签到接口，将当前用户当天签到信息保存到Redis

```java
	@Override
    public Result sign() {
        //获取当前用户
        Long userId = UserHolder.getUser().getId();
        //获取日期
        LocalDateTime now = LocalDateTime.now();
        //拼接key
        String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyy:MM"));
        String key = "sign:"+userId+keySuffix;
        //获取今天是这个月的第几天
        int dayOfMonth = now.getDayOfMonth();
        //写入Redis setbit key offset 1
        stringRedisTemplate.opsForValue().setBit(key,dayOfMonth - 1,true);
        return Result.ok();
    }
```

### 7.3 签到统计

连续签到天数：从最后一次签到开始，向前统计，直到遇到第一次未签到位置，计算总的签到次数，就是连续签到天数

本月到今天为止的所有签到数据：bitfield key get u[dayOfMonth] 0

从后向前遍历每个bit位：与1做与运算，就能得到最后一个bit位，然后右移一位，前一个bit位就成了最后一位

【实现签到统计】

统计用户截止当前时间在本月的连续签到天数

```java
	@Override
    public Result signCount() {
        //获取当前用户
        Long userId = UserHolder.getUser().getId();
        //获取日期
        LocalDateTime now = LocalDateTime.now();
        //拼接key
        String keySuffix = now.format(DateTimeFormatter.ofPattern(":yyyy:MM"));
        String key = "sign:"+userId+keySuffix;
        //获取今天是这个月的第几天
        int dayOfMonth = now.getDayOfMonth();
        //获取本月截止今天为止的所有签到记录，返回一个十进制数字
        List<Long> results = stringRedisTemplate.opsForValue().bitField(
                key,
                BitFieldSubCommands.create()
                        .get(BitFieldSubCommands.BitFieldType.unsigned(dayOfMonth)).valueAt(0)
        );//because the command: bitfield, 可以同时执行多个子命令。所以得到List集合
        if (results == null || results.isEmpty()) {
            return Result.ok(0);
        }
        Long result = results.get(0);
        if (result == null || result == 0) {
            return Result.ok(0);
        }
        //循环遍历这个数字
        int count = 0;
        while (true) {
            //让这个数字与1进行与运算，得到数字的最后一个比特位
            //判断是否为0
            if ((result & 1) == 0) {
                //为0，未签到，结束
                break;
            } else {
                //不为0.已签到，计数器+1
                count++;
            }
            //然后把数字右移一位，开始判断前一个比特位
            result >>>= 1;
        }
        return Result.ok(count);
    }
```

## 8. UV统计

### 8.1 HyperLogLog用法

UV：Unique Visitor，独立访问量，指通过互联网访问，浏览这个网页的自然人。一天内同一个用户多次访问该网站只记录一次

PV：Page View，页面访问量或点击量，用户每访问一次网站的一个页面，记录一次PV，用户多次打开页面，则多次记录PV，用来衡量网站的流量

UV统计在服务端做有点麻烦，要判断该用户是否已经统计过了，需要将统计过的用户信息保存，如果每个用户都保存在Redis，那么数据量会非常庞大

HyperLogLog（HLL）是从LogLog算法派生而来的概率算法，用于确定非常大的集合的技术，而不需要存储其所有值。

相关算法请参考[博客](https://juejin.cn/post/6844903785744056333#heading-0)

Redis的HLL是基于String结构实现的，单个HLL的内存永远小于16kb，内存很小，代价是其测量结果是有概率性的，有小于0.81%的误差，对于UV统计完全可以忽略

常用命令：

* PFADD
* PFCOUNT
* PFMERGE

### 8.2 实现UV统计

直接利用单元测试，向hyperLogLog添加百万数据，看看内存占用和统计效果

```java
	@Test
    public void TestHyperLogLog() {
        String[] values = new String[1000];
        int j = 0;
        for (int i = 0; i < 1000000; i++) {
            j = i % 1000;
            values[j] = "user_" + i;
            if (j == 999) {
                stringRedisTemplate.opsForHyperLogLog().add("hll1",values);
            }
        }
        Long count = stringRedisTemplate.opsForHyperLogLog().size("hll1");
        System.out.println("count == "+count);
    }
```

------



# 三. 高级篇

## ①. 分布式缓存

【单点Redis问题】

* 数据丢失问题
* 并发能力问题
* 故障恢复问题
* 存储能力问题

对应解决方案：

* 实现Redis数据持久化
* 搭建主从集群，实现读写分离
* 利用Redis哨兵机制，实现健康检测和自动恢复
* 搭建分片集群，利用插槽机制实现动态扩容

### 1. Redis持久化

Redis有两种持久化方案：

- RDB持久化
- AOF持久化

#### 1.1.RDB持久化

RDB全称Redis Database Backup file（Redis数据备份文件），也被叫做Redis数据快照。简单来说就是把内存中的所有数据都记录到磁盘中。当Redis实例故障重启后，从磁盘读取快照文件，恢复数据。快照文件称为RDB文件，默认是保存在当前运行目录。

##### 1.1.1.执行时机

RDB持久化在四种情况下会执行：

- 执行save命令
- 执行bgsave命令
- Redis停机时
- 触发RDB条件时

**1）save命令**

执行下面的命令，可以立即执行一次RDB：

![image-20210725144536958](./imgs/image-20210725144536958.png)

save命令会导致主进程执行RDB，这个过程中其它所有命令都会被阻塞。只有在数据迁移时可能用到。

**2）bgsave命令**

下面的命令可以异步执行RDB：

![image-20210725144725943](./imgs/image-20210725144725943.png)

这个命令执行后会开启独立进程完成RDB，主进程可以持续处理用户请求，不受影响。

**3）停机时**

Redis停机时会执行一次save命令，实现RDB持久化。

**4）触发RDB条件**

Redis内部有触发RDB的机制，可以在redis.conf文件中找到，格式如下：

```properties
# 900秒内，如果至少有1个key被修改，则执行bgsave ， 如果是save "" 则表示禁用RDB
save 900 1  
save 300 10  
save 60 10000 
```

RDB的其它配置也可以在redis.conf文件中设置：

```properties
# 是否压缩 ,建议不开启，压缩也会消耗cpu，磁盘的话不值钱
rdbcompression yes

# RDB文件名称
dbfilename dump.rdb  

# 文件保存的路径目录
dir ./ 
```

##### 1.1.2.RDB原理

bgsave开始时会fork主进程得到子进程，子进程共享主进程的内存数据。完成fork后读取内存数据并写入 RDB 文件。

fork采用的是copy-on-write技术：

- 当主进程执行读操作时，访问共享内存；
- 当主进程执行写操作时，则会拷贝一份数据，执行写操作。

![image-20210725151319695](./imgs/image-20210725151319695.png)

##### 1.1.3.小结

RDB方式bgsave的基本流程？

- fork主进程得到一个子进程，共享内存空间
- 子进程读取内存数据并写入新的RDB文件
- 用新RDB文件替换旧的RDB文件

RDB会在什么时候执行？save 60 1000代表什么含义？

- 默认是服务停止时
- 代表60秒内至少执行1000次修改则触发RDB

RDB的缺点？

- RDB执行间隔时间长，两次RDB之间写入数据有丢失的风险
- fork子进程、压缩、写出RDB文件都比较耗时

#### 1.2.AOF持久化

##### 1.2.1.AOF原理

AOF全称为Append Only File（追加文件）。Redis处理的每一个写命令都会记录在AOF文件，可以看做是命令日志文件。

![image-20210725151543640](./imgs/image-20210725151543640.png)

##### 1.2.2.AOF配置

AOF默认是关闭的，需要修改redis.conf配置文件来开启AOF：

```properties
# 是否开启AOF功能，默认是no
appendonly yes
# AOF文件的名称
appendfilename "appendonly.aof"
```

AOF的命令记录的频率也可以通过redis.conf文件来配：

```properties
# 表示每执行一次写命令，立即记录到AOF文件
appendfsync always 
# 写命令执行完先放入AOF缓冲区，然后表示每隔1秒将缓冲区数据写到AOF文件，是默认方案
appendfsync everysec 
# 写命令执行完先放入AOF缓冲区，由操作系统决定何时将缓冲区内容写回磁盘
appendfsync no
```

三种策略对比：

![image-20210725151654046](./imgs/image-20210725151654046.png)

##### 1.2.3.AOF文件重写

因为是记录命令，AOF文件会比RDB文件大的多。而且AOF会记录对同一个key的多次写操作，但只有最后一次写操作才有意义。通过执行bgrewriteaof命令，可以让AOF文件执行重写功能，用最少的命令达到相同效果。

![image-20210725151729118](./imgs/image-20210725151729118.png)

如图，AOF原本有三个命令，但是`set num 123 和 set num 666`都是对num的操作，第二次会覆盖第一次的值，因此第一个命令记录下来没有意义。

所以重写命令后，AOF文件内容就是：`mset name jack num 666`

Redis也会在触发阈值时自动去重写AOF文件。阈值也可以在redis.conf中配置：

```properties
# AOF文件比上次文件 增长超过多少百分比则触发重写
auto-aof-rewrite-percentage 100
# AOF文件体积最小多大以上才触发重写 
auto-aof-rewrite-min-size 64mb 
```

#### 1.3.RDB与AOF对比

RDB和AOF各有自己的优缺点，如果对数据安全性要求较高，在实际开发中往往会**结合**两者来使用。

![image-20210725151940515](./imgs/image-20210725151940515.png)

### 2. Redis主从

#### 2.1.搭建主从架构

单节点Redis的并发能力是有上限的，要进一步提高Redis的并发能力，就需要搭建主从集群，实现读写分离。

![image-20210725152037611](./imgs/image-20210725152037611.png)

具体搭建流程参考课前资料[《Redis集群.md》](./Redis集群.md)：

![image-20210725152052501](./imgs/image-20210725152052501.png)

#### 2.2.主从数据同步原理

##### 2.2.1.全量同步

主从第一次建立连接时，会执行**全量同步**，将master节点的所有数据都拷贝给slave节点，流程：

![image-20210725152222497](./imgs/image-20210725152222497.png)

这里有一个问题，master如何得知salve是第一次来连接呢？？

有几个概念，可以作为判断依据：

- **Replication Id**：简称replid，是**数据集的标记**，id一致则说明是同一数据集。每一个master都有唯一的replid，**slave则会继承master节点的replid**
- **offset**：偏移量，随着记录在repl_baklog中的数据增多而逐渐增大。slave完成同步时也会记录当前同步的offset。如果slave的offset小于master的offset，说明slave数据落后于master，需要更新。

因此slave做数据同步，必须向master声明自己的replication id 和offset，master才可以判断到底需要同步哪些数据。

因为slave原本也是一个master，有自己的replid和offset，当第一次变成slave，与master建立连接时，发送的replid和offset是自己的replid和offset。

master判断发现slave发送来的replid与自己的不一致，说明这是一个全新的slave，就知道要做全量同步了。

master会将自己的replid和offset都发送给这个slave，slave保存这些信息。以后slave的replid就与master一致了。

因此，**master判断一个节点是否是第一次同步的依据，就是看replid是否一致**。

如图：

![image-20210725152700914](./imgs/image-20210725152700914.png)

完整流程描述：

- slave节点请求增量同步
- master节点判断replid，发现不一致，拒绝增量同步
- master将完整内存数据生成RDB，发送RDB到slave
- slave清空本地数据，加载master的RDB
- master将RDB期间的命令记录在repl_baklog，并持续将log中的命令发送给slave
- slave执行接收到的命令，保持与master之间的同步

##### 2.2.2.增量同步

全量同步需要先做RDB，然后将RDB文件通过网络传输个slave，成本太高了。因此除了第一次做全量同步，其它大多数时候slave与master都是做**增量同步**。

什么是增量同步？就是只更新slave与master存在差异的部分数据。如图：

![image-20210725153201086](./imgs/image-20210725153201086.png)

那么master怎么知道slave与自己的数据差异在哪里呢?

##### 2.2.3.repl_backlog原理

master怎么知道slave与自己的数据差异在哪里呢?

这就要说到全量同步时的**repl_baklog文件**了。

这个文件**是一个固定大小的数组，只不过数组是环形**，也就是说**角标到达数组末尾后，会再次从0开始读写**，这样数组头部的数据就会被覆盖。

repl_baklog中会记录Redis处理过的命令日志及offset，包括master当前的offset，和slave已经拷贝到的offset：

![image-20210725153359022](./imgs/image-20210725153359022.png)

slave与master的offset之间的差异，就是salve需要增量拷贝的数据了。

随着不断有数据写入，master的offset逐渐变大，slave也不断的拷贝，追赶master的offset：

![image-20210725153524190](./imgs/image-20210725153524190.png)

直到数组被填满：

![image-20210725153715910](./imgs/image-20210725153715910.png)

此时，如果有新的数据写入，就会覆盖数组中的旧数据。不过，旧的数据只要是绿色的，说明是已经被同步到slave的数据，即便被覆盖了也没什么影响。因为未同步的仅仅是红色部分。

但是，如果slave出现网络阻塞，导致master的offset远远超过了slave的offset：

![image-20210725153937031](./imgs/image-20210725153937031.png)

如果master继续写入新数据，其offset就会覆盖旧的数据，直到将slave现在的offset也覆盖：

![image-20210725154155984](./imgs/image-20210725154155984.png)

棕色框中的红色部分，就是尚未同步，但是却已经被覆盖的数据。此时如果slave恢复，需要同步，却发现自己的**offset都没有了，无法完成增量同步了。只能做全量同步**。

![image-20210725154216392](./imgs/image-20210725154216392.png)

#### 2.3.主从同步优化

主从同步可以保证主从数据的一致性，非常重要。

可以从以下几个方面来优化Redis主从就集群：

- 在master中配置`repl-diskless-sync yes`启用无磁盘复制，避免全量同步时的磁盘IO（提高全量同步性能）
- Redis单节点上的内存占用不要太大，减少RDB导致的过多磁盘IO（提高全量同步性能）
- 适当提高repl_baklog的大小，发现slave宕机时尽快实现故障恢复，尽可能避免全量同步
- 限制一个master上的slave节点数量，如果实在是太多slave，则可以**采用主-从-从链式结构，减少master压力**

主-从-从架构图：

![image-20210725154405899](./imgs/image-20210725154405899.png)

#### 2.4.小结

简述全量同步和增量同步区别？

- 全量同步：master将完整内存数据生成RDB，发送RDB到slave。后续命令则记录在repl_baklog，逐个发送给slave。
- 增量同步：slave提交自己的offset到master，master获取repl_baklog中从offset之后的命令给slave

什么时候执行全量同步？

- slave节点第一次连接master节点时
- slave节点断开时间太久，repl_baklog中的offset已经被覆盖时

什么时候执行增量同步？

- slave节点断开又恢复，并且在repl_baklog中能找到offset时

### 3. Redis哨兵

Redis提供了哨兵（Sentinel）机制来实现主从集群的自动故障恢复。

#### 3.1.哨兵原理

##### 3.1.1.集群结构和作用

哨兵的结构如图：

![image-20210725154528072](./imgs/image-20210725154528072.png)

哨兵的作用如下：

- **监控**：Sentinel 会不断检查您的master和slave是否按预期工作
- **自动故障恢复**：如果master故障，Sentinel会将一个slave提升为master。当故障实例恢复后也以新的master为主
- **通知**：Sentinel充当Redis客户端的服务发现来源，当集群发生故障转移时，会将最新信息推送给Redis的客户端

##### 3.1.2.集群监控原理

Sentinel**基于心跳机制监测服务状态**，每隔1秒向集群的每个实例发送ping命令：

•主观下线：如果**某sentinel节点发现某实例未在规定时间响应**，则认为该实例**主观下线**。

•客观下线：若**超过指定数量（quorum）的sentinel都认为该实例主观下线**，则该实例**客观下线**。quorum值最好超过Sentinel实例数量的一半。

![image-20210725154632354](./imgs/image-20210725154632354.png)

##### 3.1.3.集群故障恢复原理

一旦发现master故障，sentinel需要在salve中选择一个作为新的master，选择依据是这样的：

- 首先会**判断slave节点与master节点断开时间长短**，如果超过指定值（down-after-milliseconds * 10）则会排除该slave节点
- 然后**判断slave节点的slave-priority值**，越小优先级越高，如果是0则永不参与选举
- 如果slave-prority一样，则**判断slave节点的offset值**，越大说明数据越新，优先级越高
- 最后是**判断slave节点的运行id大小**，越小优先级越高。

当选出一个新的master后，该如何实现切换呢？

流程如下：

- sentinel给备选的slave1节点（端口7002）发送slaveof no one命令，让该节点成为master
- sentinel给所有其它slave发送slaveof 192.168.150.101 7002 命令，让这些slave成为新master的从节点，开始从新的master上同步数据。
- 最后，sentinel将故障节点标记为slave，当故障节点恢复后会自动成为新的master的slave节点

![image-20210725154816841](./imgs/image-20210725154816841.png)

##### 3.1.4.小结

Sentinel的三个作用是什么？

- 监控
- 故障转移
- 通知

Sentinel如何判断一个redis实例是否健康？

- 每隔1秒发送一次ping命令，如果超过一定时间没有相向则认为是主观下线
- 如果大多数sentinel都认为实例主观下线，则判定服务下线

故障转移步骤有哪些？

- 首先选定一个slave作为新的master，执行slaveof no one
- 然后让所有节点都执行slaveof 新master
- 修改故障节点配置，添加slaveof 新master

#### 3.2.搭建哨兵集群

具体搭建流程参考课前资料[《Redis集群.md》](./Redis集群.md)：

![image-20210725155019276](./imgs/image-20210725155019276.png)

#### 3.3.RedisTemplate

在Sentinel集群监管下的Redis主从集群，其节点会因为自动故障转移而发生变化，Redis的客户端必须感知这种变化，及时更新连接信息。Spring的RedisTemplate底层利用lettuce实现了节点的感知和自动切换。

下面，我们通过一个测试来实现RedisTemplate集成哨兵机制。

##### 3.3.1.导入Demo工程

首先，我们引入课前资料提供的Demo工程：

![image-20210725155124958](./imgs/image-20210725155124958.png)

##### 3.3.2.引入依赖

在项目的pom文件中引入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

##### 3.3.3.配置Redis地址

然后在配置文件application.yml中指定redis的sentinel相关信息：

```yml
spring:
  redis:
    sentinel:
      master: mymaster
      nodes:
        - 192.168.150.101:27001
        - 192.168.150.101:27002
        - 192.168.150.101:27003
```

##### 3.3.4.配置读写分离

在项目的启动类中，添加一个新的bean：

```java
@Bean
public LettuceClientConfigurationBuilderCustomizer clientConfigurationBuilderCustomizer(){
    return clientConfigurationBuilder -> clientConfigurationBuilder.readFrom(ReadFrom.REPLICA_PREFERRED);
}
```

这个bean中配置的就是读写策略，包括四种：

- MASTER：从主节点读取
- MASTER_PREFERRED：优先从master节点读取，master不可用才读取replica
- REPLICA：从slave（replica）节点读取
- REPLICA _PREFERRED：优先从slave（replica）节点读取，所有的slave都不可用才读取master

### 4. Redis分片集群

#### 4.1.搭建分片集群

主从和哨兵可以解决高可用、高并发读的问题。但是依然有两个问题没有解决：

- 海量数据存储问题
- 高并发写的问题

使用分片集群可以解决上述问题，如图:

![image-20210725155747294](./imgs/image-20210725155747294.png)

分片集群特征：

- 集群中有多个master，每个master保存不同数据
- 每个master都可以有多个slave节点
- master之间通过ping监测彼此健康状态
- 客户端请求可以访问集群任意节点，最终都会被转发到正确节点

具体搭建流程参考课前资料[《Redis集群.md》](./Redis集群.md)：

![image-20210725155806288](./imgs/image-20210725155806288.png)

#### 4.2.散列插槽

##### 4.2.1.插槽原理

Redis会把每一个master节点映射到0~16383共16384个插槽（hash slot）上，查看集群信息时就能看到：

![image-20210725155820320](./imgs/image-20210725155820320.png)

数据key不是与节点绑定，而是与插槽绑定。redis会根据key的有效部分计算插槽值，分两种情况：

- key中包含"{}"，且“{}”中至少包含1个字符，“{}”中的部分是有效部分
- key中不包含“{}”，整个key都是有效部分

例如：key是num，那么就根据num计算，如果是{itcast}num，则根据itcast计算。计算方式是利用CRC16算法得到一个hash值，然后对16384取余，得到的结果就是slot值。

![image-20210725155850200](./imgs/image-20210725155850200.png)

如图，在7001这个节点执行set a 1时，对a做hash运算，对16384取余，得到的结果是15495，因此要存储到103节点。

到了7003后，执行`get num`时，对num做hash运算，对16384取余，得到的结果是2765，因此需要切换到7001节点

##### 4.2.2.小结

Redis如何判断某个key应该在哪个实例？

- 将16384个插槽分配到不同的实例
- 根据key的有效部分计算哈希值，对16384取余
- 余数作为插槽，寻找插槽所在实例即可

如何将同一类数据固定的保存在同一个Redis实例？

- 这一类数据使用相同的有效部分，例如key都以{typeId}为前缀

#### 4.3.集群伸缩

redis-cli --cluster提供了很多操作集群的命令，可以通过下面方式查看：

![image-20210725160138290](./imgs/image-20210725160138290.png)

比如，添加节点的命令：

![image-20210725160448139](./imgs/image-20210725160448139.png)

##### 4.3.1.需求分析

需求：向集群中添加一个新的master节点，并向其中存储 num = 10

- 启动一个新的redis实例，端口为7004
- 添加7004到之前的集群，并作为一个master节点
- 给7004节点分配插槽，使得num这个key可以存储到7004实例

这里需要两个新的功能：

- 添加一个节点到集群中
- 将部分插槽分配到新插槽

##### 4.3.2.创建新的redis实例

创建一个文件夹：

```sh
mkdir 7004
```

拷贝配置文件：

```sh
cp redis.conf /7004
```

修改配置文件：

```sh
sed /s/6379/7004/g 7004/redis.conf
```

启动

```sh
redis-server 7004/redis.conf
```

##### 4.3.3.添加新节点到redis

添加节点的语法如下：

![image-20210725160448139](./imgs/image-20210725160448139.png)

执行命令：

```sh
redis-cli --cluster add-node  192.168.150.101:7004 192.168.150.101:7001
```

通过命令查看集群状态：

```sh
redis-cli -p 7001 cluster nodes
```

如图，7004加入了集群，并且默认是一个master节点：

![image-20210725161007099](./imgs/image-20210725161007099.png)

但是，可以看到7004节点的插槽数量为0，因此没有任何数据可以存储到7004上

##### 4.3.4.转移插槽

我们要将num存储到7004节点，因此需要先看看num的插槽是多少：

![image-20210725161241793](./imgs/image-20210725161241793.png)

如上图所示，num的插槽为2765.

我们可以将0~3000的插槽从7001转移到7004，命令格式如下：

![image-20210725161401925](./imgs/image-20210725161401925.png)

具体命令如下：

建立连接：

![image-20210725161506241](./imgs/image-20210725161506241.png)

得到下面的反馈：

![image-20210725161540841](./imgs/image-20210725161540841.png)

询问要移动多少个插槽，我们计划是3000个：

新的问题来了：

![image-20210725161637152](./imgs/image-20210725161637152.png)

那个node来接收这些插槽？？

显然是7004，那么7004节点的id是多少呢？

![image-20210725161731738](./imgs/image-20210725161731738.png)

复制这个id，然后拷贝到刚才的控制台后：

![image-20210725161817642](./imgs/image-20210725161817642.png)

这里询问，你的插槽是从哪里移动过来的？

- all：代表全部，也就是三个节点各转移一部分
- 具体的id：目标节点的id
- done：没有了

这里我们要从7001获取，因此填写7001的id：

![image-20210725162030478](./imgs/image-20210725162030478.png)

填完后，点击done，这样插槽转移就准备好了：

![image-20210725162101228](./imgs/image-20210725162101228.png)

确认要转移吗？输入yes：

然后，通过命令查看结果：

![image-20210725162145497](./imgs/image-20210725162145497.png)

可以看到：

![image-20210725162224058](./imgs/image-20210725162224058.png)

目的达成。

#### 4.4.故障转移

集群初识状态是这样的：

![image-20210727161152065](./imgs/image-20210727161152065.png)

其中7001、7002、7003都是master，我们计划让7002宕机。

##### 4.4.1.自动故障转移

当集群中有一个master宕机会发生什么呢？

直接停止一个redis实例，例如7002：

```sh
redis-cli -p 7002 shutdown
```

1）首先是该实例与其它实例失去连接

2）然后是疑似宕机：

![image-20210725162319490](./imgs/image-20210725162319490.png)

3）最后是确定下线，自动提升一个slave为新的master：

![image-20210725162408979](./imgs/image-20210725162408979.png)

4）当7002再次启动，就会变为一个slave节点了：

![image-20210727160803386](./imgs/image-20210727160803386.png)

##### 4.4.2.手动故障转移

利用cluster failover命令可以手动让集群中的某个master宕机，切换到执行cluster failover命令的这个slave节点，实现无感知的数据迁移。其流程如下：

![image-20210725162441407](./imgs/image-20210725162441407.png)

这种failover命令可以指定三种模式：

- 缺省：默认的流程，如图1~6歩
- force：省略了对offset的一致性校验
- takeover：直接执行第5歩，忽略数据一致性、忽略master状态和其它master的意见

**案例需求**：在7002这个slave节点执行手动故障转移，重新夺回master地位

步骤如下：

1）利用redis-cli连接7002这个节点

2）执行cluster failover命令

如图：

![image-20210727160037766](./imgs/image-20210727160037766.png)

效果：

![image-20210727161152065](./imgs/image-20210727161152065.png)

#### 4.5.RedisTemplate访问分片集群

RedisTemplate底层同样基于lettuce实现了分片集群的支持，而使用的步骤与哨兵模式基本一致：

1）引入redis的starter依赖

2）配置分片集群地址

3）配置读写分离

与哨兵模式相比，其中只有分片集群的配置方式略有差异，如下：

```yaml
spring:
  redis:
    cluster:
      nodes:
        - 192.168.150.101:7001
        - 192.168.150.101:7002
        - 192.168.150.101:7003
        - 192.168.150.101:8001
        - 192.168.150.101:8002
        - 192.168.150.101:8003
```

## ②. 多级缓存

### 1.什么是多级缓存

传统的缓存策略一般是请求到达Tomcat后，先查询Redis，如果未命中则查询数据库，如图：

![image-20210821075259137](./imgs/image-20210821075259137.png)

存在下面的问题：

•请求要经过Tomcat处理，Tomcat的性能成为整个系统的瓶颈

•Redis缓存失效时，会对数据库产生冲击

多级缓存就是充分利用请求处理的每个环节，分别添加缓存，减轻Tomcat压力，提升服务性能：

- 浏览器访问静态资源时，优先读取浏览器本地缓存
- 访问非静态资源（ajax查询数据）时，访问服务端
- 请求到达Nginx后，优先读取Nginx本地缓存
- 如果Nginx本地缓存未命中，则去直接查询Redis（不经过Tomcat）
- 如果Redis查询未命中，则查询Tomcat
- 请求进入Tomcat后，优先查询JVM进程缓存
- 如果JVM进程缓存未命中，则查询数据库

![image-20210821075558137](./imgs/image-20210821075558137.png)

在多级缓存架构中，Nginx内部需要编写本地缓存查询、Redis查询、Tomcat查询的业务逻辑，因此这样的nginx服务不再是一个**反向代理服务器**，而是一个编写**业务的Web服务器了**。

因此这样的业务Nginx服务也需要搭建集群来提高并发，再有专门的nginx服务来做反向代理，如图：

![image-20210821080511581](./imgs/image-20210821080511581.png)

另外，我们的Tomcat服务将来也会部署为集群模式：

![image-20210821080954947](./imgs/image-20210821080954947.png)

可见，多级缓存的关键有两个：

- 一个是在nginx中编写业务，实现nginx本地缓存、Redis、Tomcat的查询
- 另一个就是在Tomcat中实现JVM进程缓存

其中Nginx编程则会用到OpenResty框架结合Lua这样的语言。

这也是今天课程的难点和重点。

### 2.JVM进程缓存

为了演示多级缓存的案例，我们先准备一个商品查询的业务。

#### 2.1.导入案例

参考：[《案例导入说明.md》](./案例导入说明.md)

![image-20210821081418456](./imgs/image-20210821081418456.png)

#### 2.2.初识Caffeine

缓存在日常开发中启动至关重要的作用，由于是存储在内存中，数据的读取速度是非常快的，能大量减少对数据库的访问，减少数据库的压力。我们把缓存分为两类：

- 分布式缓存，例如Redis：
  - 优点：存储容量更大、可靠性更好、可以在集群间共享
  - 缺点：访问缓存有网络开销
  - 场景：缓存数据量较大、可靠性要求较高、需要在集群间共享
- 进程本地缓存，例如HashMap、GuavaCache：
  - 优点：读取本地内存，没有网络开销，速度更快
  - 缺点：存储容量有限、可靠性较低、无法共享
  - 场景：性能要求较高，缓存数据量较小

我们今天会利用Caffeine框架来实现JVM进程缓存。

**Caffeine**是一个基于Java8开发的，提供了近乎最佳命中率的高性能的本地缓存库。目前Spring内部的缓存使用的就是Caffeine。GitHub地址：https://github.com/ben-manes/caffeine

Caffeine的性能非常好，下图是官方给出的性能对比：

![image-20210821081826399](./imgs/image-20210821081826399.png)

可以看到Caffeine的性能遥遥领先！

缓存使用的基本API：

```java
@Test
void testBasicOps() {
    // 构建cache对象
    Cache<String, String> cache = Caffeine.newBuilder().build();

    // 存数据
    cache.put("gf", "迪丽热巴");

    // 取数据
    String gf = cache.getIfPresent("gf");
    System.out.println("gf = " + gf);

    // 取数据，包含两个参数：
    // 参数一：缓存的key
    // 参数二：Lambda表达式，表达式参数就是缓存的key，方法体是查询数据库的逻辑
    // 优先根据key查询JVM缓存，如果未命中，则执行参数二的Lambda表达式
    String defaultGF = cache.get("defaultGF", key -> {
        // 根据key去数据库查询数据
        return "柳岩";
    });
    System.out.println("defaultGF = " + defaultGF);
}
```

Caffeine既然是缓存的一种，肯定需要有缓存的清除策略，不然的话内存总会有耗尽的时候。

Caffeine提供了三种缓存驱逐策略：

- **基于容量**：设置缓存的数量上限

  ```java
  // 创建缓存对象
  Cache<String, String> cache = Caffeine.newBuilder()
      .maximumSize(1) // 设置缓存大小上限为 1
      .build();
  ```

- **基于时间**：设置缓存的有效时间

  ```java
  // 创建缓存对象
  Cache<String, String> cache = Caffeine.newBuilder()
      // 设置缓存有效期为 10 秒，从最后一次写入开始计时 
      .expireAfterWrite(Duration.ofSeconds(10)) 
      .build();
  ```

- **基于引用**：设置缓存为软引用或弱引用，利用GC来回收缓存数据。性能较差，不建议使用。

> **注意**：在默认情况下，当一个缓存元素过期的时候，Caffeine不会自动立即将其清理和驱逐。而是在一次读或写操作后，或者在空闲时间完成对失效数据的驱逐。

#### 2.3.实现JVM进程缓存

##### 2.3.1.需求

利用Caffeine实现下列需求：

- 给根据id查询商品的业务添加缓存，缓存未命中时查询数据库
- 给根据id查询商品库存的业务添加缓存，缓存未命中时查询数据库
- 缓存初始大小为100
- 缓存上限为10000

##### 2.3.2.实现

首先，我们需要定义两个Caffeine的缓存对象，分别保存商品、库存的缓存数据。

在item-service的`com.heima.item.config`包下定义`CaffeineConfig`类：

```java
package com.heima.item.config;

import com.github.benmanes.caffeine.cache.Cache;
import com.github.benmanes.caffeine.cache.Caffeine;
import com.heima.item.pojo.Item;
import com.heima.item.pojo.ItemStock;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CaffeineConfig {

    @Bean
    public Cache<Long, Item> itemCache(){
        return Caffeine.newBuilder()
                .initialCapacity(100)
                .maximumSize(10_000)
                .build();
    }

    @Bean
    public Cache<Long, ItemStock> stockCache(){
        return Caffeine.newBuilder()
                .initialCapacity(100)
                .maximumSize(10_000)
                .build();
    }
}
```

然后，修改item-service中的`com.heima.item.web`包下的ItemController类，添加缓存逻辑：

```java
@RestController
@RequestMapping("item")
public class ItemController {

    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;

    @Autowired
    private Cache<Long, Item> itemCache;
    @Autowired
    private Cache<Long, ItemStock> stockCache;
    
    // ...其它略
    
    @GetMapping("/{id}")
    public Item findById(@PathVariable("id") Long id) {
        return itemCache.get(id, key -> itemService.query()
                .ne("status", 3).eq("id", key)
                .one()
        );
    }

    @GetMapping("/stock/{id}")
    public ItemStock findStockById(@PathVariable("id") Long id) {
        return stockCache.get(id, key -> stockService.getById(key));
    }
}
```

### 3.Lua语法入门

Nginx编程需要用到Lua语言，因此我们必须先入门Lua的基本语法。

#### 3.1.初识Lua

Lua 是一种轻量小巧的脚本语言，用标准C语言编写并以源代码形式开放， 其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能。官网：https://www.lua.org/

![image-20210821091437975](./imgs/image-20210821091437975.png)

Lua经常嵌入到C语言开发的程序中，例如游戏开发、游戏插件等。

Nginx本身也是C语言开发，因此也允许基于Lua做拓展。

##### 3.1.1.HelloWorld

CentOS7默认已经安装了Lua语言环境，所以可以直接运行Lua代码。

1）在Linux虚拟机的任意目录下，新建一个hello.lua文件

![image-20210821091621308](./imgs/image-20210821091621308.png)

2）添加下面的内容

```lua
print("Hello World!")  
```

3）运行

![image-20210821091638140](./imgs/image-20210821091638140.png)

#### 3.2.变量和循环

学习任何语言必然离不开变量，而变量的声明必须先知道数据的类型。

##### 3.2.1.Lua的数据类型

Lua中支持的常见数据类型包括：

![image-20210821091835406](./imgs/image-20210821091835406.png)

另外，Lua提供了type()函数来判断一个变量的数据类型：

![image-20210821091904332](./imgs/image-20210821091904332.png)

##### 3.2.2.声明变量

Lua声明变量的时候无需指定数据类型，而是用local来声明变量为局部变量：

```lua
-- 声明字符串，可以用单引号或双引号，
local str = 'hello'
-- 字符串拼接可以使用 ..
local str2 = 'hello' .. 'world'
-- 声明数字
local num = 21
-- 声明布尔类型
local flag = true
```

Lua中的table类型既可以作为数组，又可以作为Java中的map来使用。数组就是特殊的table，key是数组角标而已：

```lua
-- 声明数组 ，key为角标的 table
local arr = {'java', 'python', 'lua'}
-- 声明table，类似java的map
local map =  {name='Jack', age=21}
```

Lua中的数组角标是从1开始，访问的时候与Java中类似：

```lua
-- 访问数组，lua数组的角标从1开始
print(arr[1])
```

Lua中的table可以用key来访问：

```lua
-- 访问table
print(map['name'])
print(map.name)
```

##### 3.2.3.循环

对于table，我们可以利用for循环来遍历。不过数组和普通table遍历略有差异。

遍历数组：

```lua
-- 声明数组 key为索引的 table
local arr = {'java', 'python', 'lua'}
-- 遍历数组
for index,value in ipairs(arr) do
    print(index, value) 
end
```

遍历普通table

```lua
-- 声明map，也就是table
local map = {name='Jack', age=21}
-- 遍历table
for key,value in pairs(map) do
   print(key, value) 
end
```

#### 3.3.条件控制、函数

Lua中的条件控制和函数声明与Java类似。

##### 3.3.1.函数

定义函数的语法：

```lua
function 函数名( argument1, argument2..., argumentn)
    -- 函数体
    return 返回值
end
```

例如，定义一个函数，用来打印数组：

```lua
function printArr(arr)
    for index, value in ipairs(arr) do
        print(value)
    end
end
```

##### 3.3.2.条件控制

类似Java的条件控制，例如if、else语法：

```lua
if(布尔表达式)
then
   --[ 布尔表达式为 true 时执行该语句块 --]
else
   --[ 布尔表达式为 false 时执行该语句块 --]
end
```

与java不同，布尔表达式中的逻辑运算是基于英文单词：

![image-20210821092657918](./imgs/image-20210821092657918.png)

##### 3.3.3.案例

需求：自定义一个函数，可以打印table，当参数为nil时，打印错误信息

```lua
function printArr(arr)
    if not arr then
        print('数组不能为空！')
    end
    for index, value in ipairs(arr) do
        print(value)
    end
end
```

### 4.实现多级缓存

多级缓存的实现离不开Nginx编程，而Nginx编程又离不开OpenResty。

#### 4.1.安装OpenResty

OpenResty® 是一个基于 Nginx的高性能 Web 平台，用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。具备下列特点：

- 具备Nginx的完整功能
- 基于Lua语言进行扩展，集成了大量精良的 Lua 库、第三方模块
- 允许使用Lua**自定义业务逻辑**、**自定义库**

官方网站： https://openresty.org/cn/

![image-20210821092902946](./imgs/image-20210821092902946.png)

安装Lua可以参考课前资料提供的[《安装OpenResty.md》](./安装OpenResty.md)：

![image-20210821092941139](./imgs/image-20210821092941139.png)

#### 4.2.OpenResty快速入门

我们希望达到的多级缓存架构如图：

![yeVDlwtfMx](./imgs/yeVDlwtfMx.png)

其中：

- windows上的nginx用来做反向代理服务，将前端的查询商品的ajax请求代理到OpenResty集群
- OpenResty集群用来编写多级缓存业务

##### 4.2.1.反向代理流程

现在，商品详情页使用的是假的商品数据。不过在浏览器中，可以看到页面有发起ajax请求查询真实商品数据。

这个请求如下：

![image-20210821093144700](./imgs/image-20210821093144700.png)

请求地址是localhost，端口是80，就被windows上安装的Nginx服务给接收到了。然后代理给了OpenResty集群：

![image-20210821094447709](./imgs/image-20210821094447709.png)

我们需要在OpenResty中编写业务，查询商品数据并返回到浏览器。

但是这次，我们先在OpenResty接收请求，返回假的商品数据。

##### 4.2.2.OpenResty监听请求

OpenResty的很多功能都依赖于其目录下的Lua库，需要在nginx.conf中指定依赖库的目录，并导入依赖：

1）添加对OpenResty的Lua模块的加载

修改`/usr/local/openresty/nginx/conf/nginx.conf`文件，在其中的http下面，添加下面代码：

```nginx
#lua 模块
lua_package_path "/usr/local/openresty/lualib/?.lua;;";
#c模块     
lua_package_cpath "/usr/local/openresty/lualib/?.so;;";  
```

2）监听/api/item路径

修改`/usr/local/openresty/nginx/conf/nginx.conf`文件，在nginx.conf的server下面，添加对/api/item这个路径的监听：

```nginx
location  /api/item {
    # 默认的响应类型
    default_type application/json;
    # 响应结果由lua/item.lua文件来决定
    content_by_lua_file lua/item.lua;
}
```

这个监听，就类似于SpringMVC中的`@GetMapping("/api/item")`做路径映射。

而`content_by_lua_file lua/item.lua`则相当于调用item.lua这个文件，执行其中的业务，把结果返回给用户。相当于java中调用service。

##### 4.2.3.编写item.lua

1）在`/usr/loca/openresty/nginx`目录创建文件夹：lua

![image-20210821100755080](./imgs/image-20210821100755080.png)

2）在`/usr/loca/openresty/nginx/lua`文件夹下，新建文件：item.lua

![image-20210821100801756](./imgs/image-20210821100801756.png)

3）编写item.lua，返回假数据

item.lua中，利用ngx.say()函数返回数据到Response中

```lua
ngx.say('{"id":10001,"name":"SALSA AIR","title":"RIMOWA 21寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":17900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```

4）重新加载配置

```sh
nginx -s reload
```

刷新商品页面：http://localhost/item.html?id=1001，即可看到效果：

![image-20210821101217089](./imgs/image-20210821101217089.png)

#### 4.3.请求参数处理

上一节中，我们在OpenResty接收前端请求，但是返回的是假数据。

要返回真实数据，必须根据前端传递来的商品id，查询商品信息才可以。

那么如何获取前端传递的商品参数呢？

##### 4.3.1.获取参数的API

OpenResty中提供了一些API用来获取不同类型的前端请求参数：

![image-20210821101433528](./imgs/image-20210821101433528.png)

##### 4.3.2.获取参数并返回

在前端发起的ajax请求如图：

![image-20210821101721649](./imgs/image-20210821101721649.png)

可以看到商品id是以路径占位符方式传递的，因此可以利用正则表达式匹配的方式来获取ID

1）获取商品id

修改`/usr/loca/openresty/nginx/nginx.conf`文件中监听/api/item的代码，利用正则表达式获取ID：

```nginx
location ~ /api/item/(\d+) {
    # 默认的响应类型
    default_type application/json;
    # 响应结果由lua/item.lua文件来决定
    content_by_lua_file lua/item.lua;
}
```

2）拼接ID并返回

修改`/usr/loca/openresty/nginx/lua/item.lua`文件，获取id并拼接到结果中返回：

```lua
-- 获取商品id
local id = ngx.var[1]
-- 拼接并返回
ngx.say('{"id":' .. id .. ',"name":"SALSA AIR","title":"RIMOWA 21寸托运箱拉杆箱 SALSA AIR系列果绿色 820.70.36.4","price":17900,"image":"https://m.360buyimg.com/mobilecms/s720x720_jfs/t6934/364/1195375010/84676/e9f2c55f/597ece38N0ddcbc77.jpg!q70.jpg.webp","category":"拉杆箱","brand":"RIMOWA","spec":"","status":1,"createTime":"2019-04-30T16:00:00.000+00:00","updateTime":"2019-04-30T16:00:00.000+00:00","stock":2999,"sold":31290}')
```

3）重新加载并测试

运行命令以重新加载OpenResty配置：

```sh
nginx -s reload
```

刷新页面可以看到结果中已经带上了ID：

![image-20210821102235467](./imgs/image-20210821102235467.png)

#### 4.4.查询Tomcat

拿到商品ID后，本应去缓存中查询商品信息，不过目前我们还未建立nginx、redis缓存。因此，这里我们先根据商品id去tomcat查询商品信息。我们实现如图部分：

![image-20210821102610167](./imgs/image-20210821102610167.png)

需要注意的是，我们的OpenResty是在虚拟机，Tomcat是在Windows电脑上。两者IP一定不要搞错了。

![image-20210821102959829](./imgs/image-20210821102959829.png)

##### 4.4.1.发送http请求的API

nginx提供了内部API用以发送http请求：

```lua
local resp = ngx.location.capture("/path",{
    method = ngx.HTTP_GET,   -- 请求方式
    args = {a=1,b=2},  -- get方式传参数
})
```

返回的响应内容包括：

- resp.status：响应状态码
- resp.header：响应头，是一个table
- resp.body：响应体，就是响应数据

注意：这里的path是路径，并不包含IP和端口。这个请求会被nginx内部的server监听并处理。

但是我们希望这个请求发送到Tomcat服务器，所以还需要编写一个server来对这个路径做反向代理：

```nginx
 location /path {
     # 这里是windows电脑的ip和Java服务端口，需要确保windows防火墙处于关闭状态
     proxy_pass http://192.168.150.1:8081; 
 }
```

原理如图：

![image-20210821104149061](./imgs/image-20210821104149061.png)

##### 4.4.2.封装http工具

下面，我们封装一个发送Http请求的工具，基于ngx.location.capture来实现查询tomcat。

1）添加反向代理，到windows的Java服务

因为item-service中的接口都是/item开头，所以我们监听/item路径，代理到windows上的tomcat服务。

修改 `/usr/local/openresty/nginx/conf/nginx.conf`文件，添加一个location：

```nginx
location /item {
    proxy_pass http://192.168.150.1:8081;
}
```

以后，只要我们调用`ngx.location.capture("/item")`，就一定能发送请求到windows的tomcat服务。

2）封装工具类

之前我们说过，OpenResty启动时会加载以下两个目录中的工具文件：

![image-20210821104857413](./imgs/image-20210821104857413.png)

所以，自定义的http工具也需要放到这个目录下。

在`/usr/local/openresty/lualib`目录下，新建一个common.lua文件：

```sh
vi /usr/local/openresty/lualib/common.lua
```

内容如下:

```lua
-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
    local resp = ngx.location.capture(path,{
        method = ngx.HTTP_GET,
        args = params,
    })
    if not resp then
        -- 记录错误信息，返回404
        ngx.log(ngx.ERR, "http请求查询失败, path: ", path , ", args: ", args)
        ngx.exit(404)
    end
    return resp.body
end
-- 将方法导出
local _M = {  
    read_http = read_http
}  
return _M
```

这个工具将read_http函数封装到_M这个table类型的变量中，并且返回，这类似于导出。

使用的时候，可以利用`require('common')`来导入该函数库，这里的common是函数库的文件名。

3）实现商品查询

最后，我们修改`/usr/local/openresty/lua/item.lua`文件，利用刚刚封装的函数库实现对tomcat的查询：

```lua
-- 引入自定义common工具模块，返回值是common中返回的 _M
local common = require("common")
-- 从 common中获取read_http这个函数
local read_http = common.read_http
-- 获取路径参数
local id = ngx.var[1]
-- 根据id查询商品
local itemJSON = read_http("/item/".. id, nil)
-- 根据id查询商品库存
local itemStockJSON = read_http("/item/stock/".. id, nil)
```

这里查询到的结果是json字符串，并且包含商品、库存两个json字符串，页面最终需要的是把两个json拼接为一个json：

![image-20210821110441222](./imgs/image-20210821110441222.png)

这就需要我们先把JSON变为lua的table，完成数据整合后，再转为JSON。

##### 4.4.3.CJSON工具类

OpenResty提供了一个cjson的模块用来处理JSON的序列化和反序列化。

官方地址： https://github.com/openresty/lua-cjson/

1）引入cjson模块：

```lua
local cjson = require "cjson"
```

2）序列化：

```lua
local obj = {
    name = 'jack',
    age = 21
}
-- 把 table 序列化为 json
local json = cjson.encode(obj)
```

3）反序列化：

```lua
local json = '{"name": "jack", "age": 21}'
-- 反序列化 json为 table
local obj = cjson.decode(json);
print(obj.name)
```

##### 4.4.4.实现Tomcat查询

下面，我们修改之前的item.lua中的业务，添加json处理功能：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
-- 导入cjson库
local cjson = require('cjson')

-- 获取路径参数
local id = ngx.var[1]
-- 根据id查询商品
local itemJSON = read_http("/item/".. id, nil)
-- 根据id查询商品库存
local itemStockJSON = read_http("/item/stock/".. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)

-- 组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```

##### 4.4.5.基于ID负载均衡

刚才的代码中，我们的tomcat是单机部署。而实际开发中，tomcat一定是集群模式：

![image-20210821111023255](./imgs/image-20210821111023255.png)

因此，OpenResty需要对tomcat集群做负载均衡。

而默认的负载均衡规则是轮询模式，当我们查询/item/10001时：

- 第一次会访问8081端口的tomcat服务，在该服务内部就形成了JVM进程缓存
- 第二次会访问8082端口的tomcat服务，该服务内部没有JVM缓存（因为JVM缓存无法共享），会查询数据库
- ...

你看，因为轮询的原因，第一次查询8081形成的JVM缓存并未生效，直到下一次再次访问到8081时才可以生效，缓存命中率太低了。

怎么办？

如果能让同一个商品，每次查询时都访问同一个tomcat服务，那么JVM缓存就一定能生效了。

也就是说，我们需要根据商品id做负载均衡，而不是轮询。

###### 1）原理

nginx提供了基于请求路径做负载均衡的算法：

nginx根据请求路径做hash运算，把得到的数值对tomcat服务的数量取余，余数是几，就访问第几个服务，实现负载均衡。

例如：

- 我们的请求路径是 /item/10001
- tomcat总数为2台（8081、8082）
- 对请求路径/item/1001做hash运算求余的结果为1
- 则访问第一个tomcat服务，也就是8081

只要id不变，每次hash运算结果也不会变，那就可以保证同一个商品，一直访问同一个tomcat服务，确保JVM缓存生效。

###### 2）实现

修改`/usr/local/openresty/nginx/conf/nginx.conf`文件，实现基于ID做负载均衡。

首先，定义tomcat集群，并设置基于路径做负载均衡：

```nginx
upstream tomcat-cluster {
    hash $request_uri;
    server 192.168.150.1:8081;
    server 192.168.150.1:8082;
}
```

然后，修改对tomcat服务的反向代理，目标指向tomcat集群：

```nginx
location /item {
    proxy_pass http://tomcat-cluster;
}
```

重新加载OpenResty

```sh
nginx -s reload
```

###### 3）测试

启动两台tomcat服务：

![image-20210821112420464](./imgs/image-20210821112420464.png)

同时启动：

![image-20210821112444482](./imgs/image-20210821112444482.png)

清空日志后，再次访问页面，可以看到不同id的商品，访问到了不同的tomcat服务：

![image-20210821112559965](./imgs/image-20210821112559965.png)

![image-20210821112637430](./imgs/image-20210821112637430.png)

#### 4.5.Redis缓存预热

Redis缓存会面临冷启动问题：

**冷启动**：服务刚刚启动时，Redis中并没有缓存，如果所有商品数据都在第一次查询时添加缓存，可能会给数据库带来较大压力。

**缓存预热**：在实际开发中，我们可以利用大数据统计用户访问的热点数据，在项目启动时将这些热点数据提前查询并保存到Redis中。

我们数据量较少，并且没有数据统计相关功能，目前可以在启动时将所有数据都放入缓存中。

1）利用Docker安装Redis

```sh
docker run --name redis -p 6379:6379 -d redis redis-server --appendonly yes
```

2）在item-service服务中引入Redis依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

3）配置Redis地址

```yaml
spring:
  redis:
    host: 192.168.150.101
```

4）编写初始化类

缓存预热需要在项目启动时完成，并且必须是拿到RedisTemplate之后。

这里我们利用InitializingBean接口来实现，因为InitializingBean可以在对象被Spring创建并且成员变量全部注入后执行。

```java
package com.heima.item.config;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.heima.item.pojo.Item;
import com.heima.item.pojo.ItemStock;
import com.heima.item.service.IItemService;
import com.heima.item.service.IItemStockService;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class RedisHandler implements InitializingBean {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化缓存
        // 1.查询商品信息
        List<Item> itemList = itemService.list();
        // 2.放入缓存
        for (Item item : itemList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(item);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        }

        // 3.查询商品库存信息
        List<ItemStock> stockList = stockService.list();
        // 4.放入缓存
        for (ItemStock stock : stockList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(stock);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:stock:id:" + stock.getId(), json);
        }
    }
}
```

#### 4.6.查询Redis缓存

现在，Redis缓存已经准备就绪，我们可以再OpenResty中实现查询Redis的逻辑了。如下图红框所示：

![image-20210821113340111](./imgs/image-20210821113340111.png)

当请求进入OpenResty之后：

- 优先查询Redis缓存
- 如果Redis缓存未命中，再查询Tomcat

##### 4.6.1.封装Redis工具

OpenResty提供了操作Redis的模块，我们只要引入该模块就能直接使用。但是为了方便，我们将Redis操作封装到之前的common.lua工具库中。

修改`/usr/local/openresty/lualib/common.lua`文件：

1）引入Redis模块，并初始化Redis对象

```lua
-- 导入redis
local redis = require('resty.redis')
-- 初始化redis
local red = redis:new()
red:set_timeouts(1000, 1000, 1000)
```

2）封装函数，用来释放Redis连接，其实是放入连接池

```lua
-- 关闭redis连接的工具方法，其实是放入连接池
local function close_redis(red)
    local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
    local pool_size = 100 --连接池大小
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end
```

3）封装函数，根据key查询Redis数据

```lua
-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = " , key)
    end
    --得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end
```

4）导出

```lua
-- 将方法导出
local _M = {  
    read_http = read_http,
    read_redis = read_redis
}  
return _M
```

完整的common.lua：

```lua
-- 导入redis
local redis = require('resty.redis')
-- 初始化redis
local red = redis:new()
red:set_timeouts(1000, 1000, 1000)

-- 关闭redis连接的工具方法，其实是放入连接池
local function close_redis(red)
    local pool_max_idle_time = 10000 -- 连接的空闲时间，单位是毫秒
    local pool_size = 100 --连接池大小
    local ok, err = red:set_keepalive(pool_max_idle_time, pool_size)
    if not ok then
        ngx.log(ngx.ERR, "放入redis连接池失败: ", err)
    end
end

-- 查询redis的方法 ip和port是redis地址，key是查询的key
local function read_redis(ip, port, key)
    -- 获取一个连接
    local ok, err = red:connect(ip, port)
    if not ok then
        ngx.log(ngx.ERR, "连接redis失败 : ", err)
        return nil
    end
    -- 查询redis
    local resp, err = red:get(key)
    -- 查询失败处理
    if not resp then
        ngx.log(ngx.ERR, "查询Redis失败: ", err, ", key = " , key)
    end
    --得到的数据为空处理
    if resp == ngx.null then
        resp = nil
        ngx.log(ngx.ERR, "查询Redis数据为空, key = ", key)
    end
    close_redis(red)
    return resp
end

-- 封装函数，发送http请求，并解析响应
local function read_http(path, params)
    local resp = ngx.location.capture(path,{
        method = ngx.HTTP_GET,
        args = params,
    })
    if not resp then
        -- 记录错误信息，返回404
        ngx.log(ngx.ERR, "http查询失败, path: ", path , ", args: ", args)
        ngx.exit(404)
    end
    return resp.body
end
-- 将方法导出
local _M = {  
    read_http = read_http,
    read_redis = read_redis
}  
return _M
```

##### 4.6.2.实现Redis查询

接下来，我们就可以去修改item.lua文件，实现对Redis的查询了。

查询逻辑是：

- 根据id查询Redis
- 如果查询失败则继续查询Tomcat
- 将查询结果返回

1）修改`/usr/local/openresty/lua/item.lua`文件，添加一个查询函数：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 封装查询函数
function read_data(key, path, params)
    -- 查询本地缓存
    local val = read_redis("127.0.0.1", 6379, key)
    -- 判断查询结果
    if not val then
        ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
        -- redis查询失败，去查询http
        val = read_http(path, params)
    end
    -- 返回数据
    return val
end
```

2）而后修改商品查询、库存查询的业务：

![image-20210821114528954](./imgs/image-20210821114528954.png)

3）完整的item.lua代码：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 导入cjson库
local cjson = require('cjson')

-- 封装查询函数
function read_data(key, path, params)
    -- 查询本地缓存
    local val = read_redis("127.0.0.1", 6379, key)
    -- 判断查询结果
    if not val then
        ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
        -- redis查询失败，去查询http
        val = read_http(path, params)
    end
    -- 返回数据
    return val
end

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_data("item:id:" .. id,  "/item/" .. id, nil)
-- 查询库存信息
local stockJSON = read_data("item:stock:id:" .. id, "/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)
-- 组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```

#### 4.7.Nginx本地缓存

现在，整个多级缓存中只差最后一环，也就是nginx的本地缓存了。如图：

![image-20210821114742950](./imgs/image-20210821114742950.png)

##### 4.7.1.本地缓存API

OpenResty为Nginx提供了**shard dict**的功能，可以在nginx的多个worker之间共享数据，实现缓存功能。

1）开启共享字典，在nginx.conf的http下添加配置：

```nginx
 # 共享字典，也就是本地缓存，名称叫做：item_cache，大小150m
 lua_shared_dict item_cache 150m; 
```

2）操作共享字典：

```lua
-- 获取本地缓存对象
local item_cache = ngx.shared.item_cache
-- 存储, 指定key、value、过期时间，单位s，默认为0代表永不过期
item_cache:set('key', 'value', 1000)
-- 读取
local val = item_cache:get('key')
```

##### 4.7.2.实现本地缓存查询

1）修改`/usr/local/openresty/lua/item.lua`文件，修改read_data查询函数，添加本地缓存逻辑：

```lua
-- 导入共享词典，本地缓存
local item_cache = ngx.shared.item_cache

-- 封装查询函数
function read_data(key, expire, path, params)
    -- 查询本地缓存
    local val = item_cache:get(key)
    if not val then
        ngx.log(ngx.ERR, "本地缓存查询失败，尝试查询Redis， key: ", key)
        -- 查询redis
        val = read_redis("127.0.0.1", 6379, key)
        -- 判断查询结果
        if not val then
            ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
            -- redis查询失败，去查询http
            val = read_http(path, params)
        end
    end
    -- 查询成功，把数据写入本地缓存
    item_cache:set(key, val, expire)
    -- 返回数据
    return val
end
```

2）修改item.lua中查询商品和库存的业务，实现最新的read_data函数：

![image-20210821115108528](./imgs/image-20210821115108528.png)

其实就是多了缓存时间参数，过期后nginx缓存会自动删除，下次访问即可更新缓存。

这里给商品基本信息设置超时时间为30分钟，库存为1分钟。

因为库存更新频率较高，如果缓存时间过长，可能与数据库差异较大。

3）完整的item.lua文件：

```lua
-- 导入common函数库
local common = require('common')
local read_http = common.read_http
local read_redis = common.read_redis
-- 导入cjson库
local cjson = require('cjson')
-- 导入共享词典，本地缓存
local item_cache = ngx.shared.item_cache

-- 封装查询函数
function read_data(key, expire, path, params)
    -- 查询本地缓存
    local val = item_cache:get(key)
    if not val then
        ngx.log(ngx.ERR, "本地缓存查询失败，尝试查询Redis， key: ", key)
        -- 查询redis
        val = read_redis("127.0.0.1", 6379, key)
        -- 判断查询结果
        if not val then
            ngx.log(ngx.ERR, "redis查询失败，尝试查询http， key: ", key)
            -- redis查询失败，去查询http
            val = read_http(path, params)
        end
    end
    -- 查询成功，把数据写入本地缓存
    item_cache:set(key, val, expire)
    -- 返回数据
    return val
end

-- 获取路径参数
local id = ngx.var[1]

-- 查询商品信息
local itemJSON = read_data("item:id:" .. id, 1800,  "/item/" .. id, nil)
-- 查询库存信息
local stockJSON = read_data("item:stock:id:" .. id, 60, "/item/stock/" .. id, nil)

-- JSON转化为lua的table
local item = cjson.decode(itemJSON)
local stock = cjson.decode(stockJSON)
-- 组合数据
item.stock = stock.stock
item.sold = stock.sold

-- 把item序列化为json 返回结果
ngx.say(cjson.encode(item))
```

### 5.缓存同步

大多数情况下，浏览器查询到的都是缓存数据，如果缓存数据与数据库数据存在较大差异，可能会产生比较严重的后果。

所以我们必须保证数据库数据、缓存数据的一致性，这就是缓存与数据库的同步。

#### 5.1.数据同步策略

缓存数据同步的常见方式有三种：

**设置有效期**：给缓存设置有效期，到期后自动删除。再次查询时更新

- 优势：简单、方便
- 缺点：时效性差，缓存过期之前可能不一致
- 场景：更新频率较低，时效性要求低的业务

**同步双写**：在修改数据库的同时，直接修改缓存

- 优势：时效性强，缓存与数据库强一致
- 缺点：有代码侵入，耦合度高；
- 场景：对一致性、时效性要求较高的缓存数据

**异步通知：**修改数据库时发送事件通知，相关服务监听到通知后修改缓存数据

- 优势：低耦合，可以同时通知多个缓存服务
- 缺点：时效性一般，可能存在中间不一致状态
- 场景：时效性要求一般，有多个服务需要同步

而异步实现又可以基于MQ或者Canal来实现：

1）基于MQ的异步通知：

![image-20210821115552327](./imgs/image-20210821115552327.png)

解读：

- 商品服务完成对数据的修改后，只需要发送一条消息到MQ中。
- 缓存服务监听MQ消息，然后完成对缓存的更新

依然有少量的代码侵入。

2）基于Canal的通知

![image-20210821115719363](./imgs/image-20210821115719363.png)

解读：

- 商品服务完成商品修改后，业务直接结束，没有任何代码侵入
- Canal监听MySQL变化，当发现变化后，立即通知缓存服务
- 缓存服务接收到canal通知，更新缓存

代码零侵入

#### 5.2.安装Canal

##### 5.2.1.认识Canal

**Canal [kə'næl]**，译意为水道/管道/沟渠，canal是阿里巴巴旗下的一款开源项目，基于Java开发。基于数据库增量日志解析，提供增量数据订阅&消费。GitHub的地址：https://github.com/alibaba/canal

Canal是基于mysql的主从同步来实现的，MySQL主从同步的原理如下：

![image-20210821115914748](./imgs/image-20210821115914748.png)

- 1）MySQL master 将数据变更写入二进制日志( binary log），其中记录的数据叫做binary log events
- 2）MySQL slave 将 master 的 binary log events拷贝到它的中继日志(relay log)
- 3）MySQL slave 重放 relay log 中事件，将数据变更反映它自己的数据

而Canal就是把自己伪装成MySQL的一个slave节点，从而监听master的binary log变化。再把得到的变化信息通知给Canal的客户端，进而完成对其它数据库的同步。

![image-20210821115948395](./imgs/image-20210821115948395.png)

##### 5.2.2.安装Canal

安装和配置Canal参考[《安装Canal.md》](./安装Canal.md)：

![image-20210821120017324](./imgs/image-20210821120017324.png)

#### 5.3.监听Canal

Canal提供了各种语言的客户端，当Canal监听到binlog变化时，会通知Canal的客户端。

![image-20210821120049024](./imgs/image-20210821120049024.png)

我们可以利用Canal提供的Java客户端，监听Canal通知消息。当收到变化的消息时，完成对缓存的更新。

不过这里我们会使用GitHub上的第三方开源的canal-starter客户端。地址：https://github.com/NormanGyllenhaal/canal-client

与SpringBoot完美整合，自动装配，比官方客户端要简单好用很多。

##### 5.3.1.引入依赖：

```xml
<dependency>
    <groupId>top.javatool</groupId>
    <artifactId>canal-spring-boot-starter</artifactId>
    <version>1.2.1-RELEASE</version>
</dependency>
```

##### 5.3.2.编写配置：

```yaml
canal:
  destination: heima # canal的集群名字，要与安装canal时设置的名称一致
  server: 192.168.150.101:11111 # canal服务地址
```

##### 5.3.3.修改Item实体类

通过@Id、@Column、等注解完成Item与数据库表字段的映射：

```java
package com.heima.item.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
import lombok.Data;
import org.springframework.data.annotation.Id;
import org.springframework.data.annotation.Transient;

import javax.persistence.Column;
import java.util.Date;

@Data
@TableName("tb_item")
public class Item {
    @TableId(type = IdType.AUTO)
    @Id
    private Long id;//商品id
    @Column(name = "name")
    private String name;//商品名称
    private String title;//商品标题
    private Long price;//价格（分）
    private String image;//商品图片
    private String category;//分类名称
    private String brand;//品牌名称
    private String spec;//规格
    private Integer status;//商品状态 1-正常，2-下架
    private Date createTime;//创建时间
    private Date updateTime;//更新时间
    @TableField(exist = false)
    @Transient
    private Integer stock;
    @TableField(exist = false)
    @Transient
    private Integer sold;
}
```

##### 5.3.4.编写监听器

通过实现`EntryHandler<T>`接口编写监听器，监听Canal消息。注意两点：

- 实现类通过`@CanalTable("tb_item")`指定监听的表信息
- EntryHandler的泛型是与表对应的实体类

```java
package com.heima.item.canal;

import com.github.benmanes.caffeine.cache.Cache;
import com.heima.item.config.RedisHandler;
import com.heima.item.pojo.Item;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import top.javatool.canal.client.annotation.CanalTable;
import top.javatool.canal.client.handler.EntryHandler;

@CanalTable("tb_item")
@Component
public class ItemHandler implements EntryHandler<Item> {

    @Autowired
    private RedisHandler redisHandler;
    @Autowired
    private Cache<Long, Item> itemCache;

    @Override
    public void insert(Item item) {
        // 写数据到JVM进程缓存
        itemCache.put(item.getId(), item);
        // 写数据到redis
        redisHandler.saveItem(item);
    }

    @Override
    public void update(Item before, Item after) {
        // 写数据到JVM进程缓存
        itemCache.put(after.getId(), after);
        // 写数据到redis
        redisHandler.saveItem(after);
    }

    @Override
    public void delete(Item item) {
        // 删除数据到JVM进程缓存
        itemCache.invalidate(item.getId());
        // 删除数据到redis
        redisHandler.deleteItemById(item.getId());
    }
}
```

在这里对Redis的操作都封装到了RedisHandler这个对象中，是我们之前做缓存预热时编写的一个类，内容如下：

```java
package com.heima.item.config;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.heima.item.pojo.Item;
import com.heima.item.pojo.ItemStock;
import com.heima.item.service.IItemService;
import com.heima.item.service.IItemStockService;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class RedisHandler implements InitializingBean {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @Autowired
    private IItemService itemService;
    @Autowired
    private IItemStockService stockService;

    private static final ObjectMapper MAPPER = new ObjectMapper();

    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化缓存
        // 1.查询商品信息
        List<Item> itemList = itemService.list();
        // 2.放入缓存
        for (Item item : itemList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(item);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        }

        // 3.查询商品库存信息
        List<ItemStock> stockList = stockService.list();
        // 4.放入缓存
        for (ItemStock stock : stockList) {
            // 2.1.item序列化为JSON
            String json = MAPPER.writeValueAsString(stock);
            // 2.2.存入redis
            redisTemplate.opsForValue().set("item:stock:id:" + stock.getId(), json);
        }
    }

    public void saveItem(Item item) {
        try {
            String json = MAPPER.writeValueAsString(item);
            redisTemplate.opsForValue().set("item:id:" + item.getId(), json);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    public void deleteItemById(Long id) {
        redisTemplate.delete("item:id:" + id);
    }
}
```

## ③. Redis最佳实践

### 1、Redis键值设计

#### 1.1、优雅的key结构

Redis的Key虽然可以自定义，但最好遵循下面的几个最佳实践约定：

- 遵循基本格式：[业务名称]:[数据名]:[id]
- 长度不超过44字节
- 不包含特殊字符

例如：我们的登录业务，保存用户信息，其key可以设计成如下格式：

![image-20220521120213631](./imgs/image-20220521120213631.png)

这样设计的好处：

- 可读性强
- 避免key冲突
- 方便管理
- 更节省内存： key是string类型，底层编码包含int、embstr和raw三种。embstr在小于44字节使用，采用连续内存空间，内存占用更小。当字节数大于44字节时，会转为raw模式存储，在raw模式下，内存空间不是连续的，而是采用一个指针指向了另外一段内存空间，在这段空间里存储SDS内容，这样空间不连续，访问的时候性能也就会收到影响，还有可能产生内存碎片

![image-20220521122320482](./imgs/image-20220521122320482.png)

#### 1.2、拒绝BigKey

BigKey通常以Key的大小和Key中成员的数量来综合判定，例如：

- Key本身的数据量过大：一个String类型的Key，它的值为5 MB
- Key中的成员数过多：一个ZSET类型的Key，它的成员数量为10,000个
- Key中成员的数据量过大：一个Hash类型的Key，它的成员数量虽然只有1,000个但这些成员的Value（值）总大小为100 MB

那么如何判断元素的大小呢？redis也给我们提供了命令

![image-20220521124650117](./imgs/image-20220521124650117.png)

推荐值：

- 单个key的value小于10KB
- 对于集合类型的key，建议元素数量小于1000

##### 1.2.1、BigKey的危害

- 网络阻塞
  - 对BigKey执行读请求时，少量的QPS就可能导致带宽使用率被占满，导致Redis实例，乃至所在物理机变慢
- 数据倾斜
  - BigKey所在的Redis实例内存使用率远超其他实例，无法使数据分片的内存资源达到均衡
- Redis阻塞
  - 对元素较多的hash、list、zset等做运算会耗时较旧，使主线程被阻塞
- CPU压力
  - 对BigKey的数据序列化和反序列化会导致CPU的使用率飙升，影响Redis实例和本机其它应用

##### 1.2.2、如何发现BigKey

①redis-cli --bigkeys

利用redis-cli提供的--bigkeys参数，可以遍历分析所有key，并返回Key的整体统计信息与每个数据的Top1的big key

命令：`redis-cli -a 密码 --bigkeys`

![image-20220521133359507](./imgs/image-20220521133359507.png)

②scan扫描

自己编程，利用scan扫描Redis中的所有key，利用strlen、hlen等命令判断key的长度（此处不建议使用MEMORY USAGE）

![image-20220521133703245](./imgs/image-20220521133703245.png)

scan 命令调用完后每次会返回2个元素，第一个是下一次迭代的光标，第一次光标会设置为0，当最后一次scan 返回的光标等于0时，表示整个scan遍历结束了，第二个返回的是List，一个匹配的key的数组

```java
import com.heima.jedis.util.JedisConnectionFactory;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.ScanResult;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1.建立连接
        // jedis = new Jedis("192.168.150.101", 6379);
        jedis = JedisConnectionFactory.getJedis();
        // 2.设置密码
        jedis.auth("123321");
        // 3.选择库
        jedis.select(0);
    }

    final static int STR_MAX_LEN = 10 * 1024;
    final static int HASH_MAX_LEN = 500;

    @Test
    void testScan() {
        int maxLen = 0;
        long len = 0;

        String cursor = "0";
        do {
            // 扫描并获取一部分key
            ScanResult<String> result = jedis.scan(cursor);
            // 记录cursor
            cursor = result.getCursor();
            List<String> list = result.getResult();
            if (list == null || list.isEmpty()) {
                break;
            }
            // 遍历
            for (String key : list) {
                // 判断key的类型
                String type = jedis.type(key);
                switch (type) {
                    case "string":
                        len = jedis.strlen(key);
                        maxLen = STR_MAX_LEN;
                        break;
                    case "hash":
                        len = jedis.hlen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "list":
                        len = jedis.llen(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "set":
                        len = jedis.scard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    case "zset":
                        len = jedis.zcard(key);
                        maxLen = HASH_MAX_LEN;
                        break;
                    default:
                        break;
                }
                if (len >= maxLen) {
                    System.out.printf("Found big key : %s, type: %s, length or size: %d %n", key, type, len);
                }
            }
        } while (!cursor.equals("0"));
    }
    
    @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }

}
```

③第三方工具

- 利用第三方工具，如 Redis-Rdb-Tools 分析RDB快照文件，全面分析内存使用情况
- https://github.com/sripathikrishnan/redis-rdb-tools

④网络监控

- 自定义工具，监控进出Redis的网络数据，超出预警值时主动告警
- 一般阿里云搭建的云服务器就有相关监控页面

![image-20220521140415785](./imgs/image-20220521140415785.png)

##### 1.2.3、如何删除BigKey

BigKey内存占用较多，即便时删除这样的key也需要耗费很长时间，导致Redis主线程阻塞，引发一系列问题。

- redis 3.0 及以下版本
  - 如果是集合类型，则遍历BigKey的元素，先逐个删除子元素，最后删除BigKey

![image-20220521140621204](./imgs/image-20220521140621204.png)

- Redis 4.0以后
  - Redis在4.0后提供了异步删除的命令：unlink

#### 1.3、恰当的数据类型

例1：比如存储一个User对象，我们有三种存储方式：

①方式一：json字符串

| user:1 | {"name": "Jack", "age": 21} |
| :----: | :-------------------------: |
|        |                             |

优点：实现简单粗暴

缺点：数据耦合，不够灵活

②方式二：字段打散

| user:1:name | Jack |
| :---------: | :--: |
| user:1:age  |  21  |

优点：可以灵活访问对象任意字段

缺点：占用空间大、没办法做统一控制

③方式三：hash（推荐）

<table> <tr> <td rowspan="2">user:1</td> <td>name</td> <td>jack</td> </tr> <tr> <td>age</td> <td>21</td> </tr> </table>

优点：底层使用ziplist，空间占用小，可以灵活访问对象的任意字段

缺点：代码相对复杂

例2：假如有hash类型的key，其中有100万对field和value，field是自增id，这个key存在什么问题？如何优化？

<table> <tr style="color:red"> <td>key</td> <td>field</td> <td>value</td> </tr> <tr> <td rowspan="3">someKey</td> <td>id:0</td> <td>value0</td> </tr> <tr> <td>.....</td> <td>.....</td> </tr> <tr> <td>id:999999</td> <td>value999999</td> </tr> </table>

存在的问题：

- hash的entry数量超过500时，会使用哈希表而不是ZipList，内存占用较多
  - ![image-20220521142943350](./imgs/image-20220521142943350.png)
- 可以通过hash-max-ziplist-entries配置entry上限。但是如果entry过多就会导致BigKey问题

方案一

拆分为string类型

<table> <tr style="color:red"> <td>key</td> <td>value</td> </tr> <tr> <td>id:0</td> <td>value0</td> </tr> <tr> <td>.....</td> <td>.....</td> </tr> <tr> <td>id:999999</td> <td>value999999</td> </tr> </table>

存在的问题：

- string结构底层没有太多内存优化，内存占用较多

![image-20220521143458010](./imgs/image-20220521143458010.png)

- 想要批量获取这些数据比较麻烦

方案二

拆分为小的hash，将 id / 100 作为key， 将id % 100 作为field，这样每100个元素为一个Hash

<table> <tr style="color:red"> <td>key</td> <td>field</td> <td>value</td> </tr> <tr> <td rowspan="3">key:0</td> <td>id:00</td> <td>value0</td> </tr> <tr> <td>.....</td> <td>.....</td> </tr> <tr> <td>id:99</td> <td>value99</td> </tr> <tr> <td rowspan="3">key:1</td> <td>id:00</td> <td>value100</td> </tr> <tr> <td>.....</td> <td>.....</td> </tr> <tr> <td>id:99</td> <td>value199</td> </tr> <tr> <td colspan="3">....</td> </tr> <tr> <td rowspan="3">key:9999</td> <td>id:00</td> <td>value999900</td> </tr> <tr> <td>.....</td> <td>.....</td> </tr> <tr> <td>id:99</td> <td>value999999</td> </tr> </table>

![image-20220521144339377](./imgs/image-20220521144339377.png)

```java
package com.heima.test;

import com.heima.jedis.util.JedisConnectionFactory;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Pipeline;
import redis.clients.jedis.ScanResult;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class JedisTest {
    private Jedis jedis;

    @BeforeEach
    void setUp() {
        // 1.建立连接
        // jedis = new Jedis("192.168.150.101", 6379);
        jedis = JedisConnectionFactory.getJedis();
        // 2.设置密码
        jedis.auth("123321");
        // 3.选择库
        jedis.select(0);
    }

    @Test
    void testSetBigKey() {
        Map<String, String> map = new HashMap<>();
        for (int i = 1; i <= 650; i++) {
            map.put("hello_" + i, "world!");
        }
        jedis.hmset("m2", map);
    }

    @Test
    void testBigHash() {
        Map<String, String> map = new HashMap<>();
        for (int i = 1; i <= 100000; i++) {
            map.put("key_" + i, "value_" + i);
        }
        jedis.hmset("test:big:hash", map);
    }

    @Test
    void testBigString() {
        for (int i = 1; i <= 100000; i++) {
            jedis.set("test:str:key_" + i, "value_" + i);
        }
    }

    @Test
    void testSmallHash() {
        int hashSize = 100;
        Map<String, String> map = new HashMap<>(hashSize);
        for (int i = 1; i <= 100000; i++) {
            int k = (i - 1) / hashSize;
            int v = i % hashSize;
            map.put("key_" + v, "value_" + v);
            if (v == 0) {
                jedis.hmset("test:small:hash_" + k, map);
            }
        }
    }

    @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }
}
```

#### 1.4、总结

- Key的最佳实践
  - 固定格式：[业务名]:[数据名]:[id]
  - 足够简短：不超过44字节
  - 不包含特殊字符
- Value的最佳实践：
  - 合理的拆分数据，拒绝BigKey
  - 选择合适数据结构
  - Hash结构的entry数量不要超过1000
  - 设置合理的超时时间

### 2、批处理优化

#### 2.1、Pipeline

##### 2.1.1、我们的客户端与redis服务器是这样交互的

单个命令的执行流程

![image-20220521151459880](./imgs/image-20220521151459880.png)

N条命令的执行流程

![image-20220521151524621](./imgs/image-20220521151524621.png)

redis处理指令是很快的，主要花费的时候在于网络传输。于是乎很容易想到将多条指令批量的传输给redis

![image-20220521151902080](./imgs/image-20220521151902080.png)

##### 2.1.2、MSet

Redis提供了很多Mxxx这样的命令，可以实现批量插入数据，例如：

- mset
- hmset

利用mset批量插入10万条数据

```java
@Test
void testMxx() {
    String[] arr = new String[2000];
    int j;
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        j = (i % 1000) << 1;
        arr[j] = "test:key_" + i;
        arr[j + 1] = "value_" + i;
        if (j == 0) {
            jedis.mset(arr);
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```

##### 2.1.3、Pipeline

MSET虽然可以批处理，但是却只能操作部分数据类型，因此如果有对复杂数据类型的批处理需要，建议使用Pipeline

```java
@Test
void testPipeline() {
    // 创建管道
    Pipeline pipeline = jedis.pipelined();
    long b = System.currentTimeMillis();
    for (int i = 1; i <= 100000; i++) {
        // 放入命令到管道
        pipeline.set("test:key_" + i, "value_" + i);
        if (i % 1000 == 0) {
            // 每放入1000条命令，批量执行
            pipeline.sync();
        }
    }
    long e = System.currentTimeMillis();
    System.out.println("time: " + (e - b));
}
```

#### 2.2、集群下的批处理

如MSET或Pipeline这样的批处理需要在一次请求中携带多条命令，而此时如果Redis是一个集群，那批处理命令的多个key必须落在一个插槽中，否则就会导致执行失败。大家可以想一想这样的要求其实很难实现，因为我们在批处理时，可能一次要插入很多条数据，这些数据很有可能不会都落在相同的节点上，这就会导致报错了

这个时候，我们可以找到4种解决方案

![1653126446641](./imgs/1653126446641.png)

第一种方案：串行执行，所以这种方式没有什么意义，当然，执行起来就很简单了，缺点就是耗时过久。

第二种方案：串行slot，简单来说，就是执行前，客户端先计算一下对应的key的slot，一样slot的key就放到一个组里边，不同的，就放到不同的组里边，然后对每个组执行pipeline的批处理，他就能串行执行各个组的命令，这种做法比第一种方法耗时要少，但是缺点呢，相对来说复杂一点，所以这种方案还需要优化一下

第三种方案：并行slot，相较于第二种方案，在分组完成后串行执行，第三种方案，就变成了并行执行各个命令，所以他的耗时就非常短，但是实现呢，也更加复杂。

第四种：hash_tag，redis计算key的slot的时候，其实是根据key的有效部分来计算的，通过这种方式就能一次处理所有的key，这种方式耗时最短，实现也简单，但是如果通过操作key的有效部分，那么就会导致所有的key都落在一个节点上，产生数据倾斜的问题，所以我们推荐使用第三种方式。

##### 2.2.1 串行化执行代码实践

```java
public class JedisClusterTest {

    private JedisCluster jedisCluster;

    @BeforeEach
    void setUp() {
        // 配置连接池
        JedisPoolConfig poolConfig = new JedisPoolConfig();
        poolConfig.setMaxTotal(8);
        poolConfig.setMaxIdle(8);
        poolConfig.setMinIdle(0);
        poolConfig.setMaxWaitMillis(1000);
        HashSet<HostAndPort> nodes = new HashSet<>();
        nodes.add(new HostAndPort("192.168.150.101", 7001));
        nodes.add(new HostAndPort("192.168.150.101", 7002));
        nodes.add(new HostAndPort("192.168.150.101", 7003));
        nodes.add(new HostAndPort("192.168.150.101", 8001));
        nodes.add(new HostAndPort("192.168.150.101", 8002));
        nodes.add(new HostAndPort("192.168.150.101", 8003));
        jedisCluster = new JedisCluster(nodes, poolConfig);
    }

    @Test
    void testMSet() {
        jedisCluster.mset("name", "Jack", "age", "21", "sex", "male");

    }

    @Test
    void testMSet2() {
        Map<String, String> map = new HashMap<>(3);
        map.put("name", "Jack");
        map.put("age", "21");
        map.put("sex", "Male");
        //对Map数据进行分组。根据相同的slot放在一个分组
        //key就是slot，value就是一个组
        Map<Integer, List<Map.Entry<String, String>>> result = map.entrySet()
                .stream()
                .collect(Collectors.groupingBy(
                        entry -> ClusterSlotHashUtil.calculateSlot(entry.getKey()))
                );
        //串行的去执行mset的逻辑
        for (List<Map.Entry<String, String>> list : result.values()) {
            String[] arr = new String[list.size() * 2];
            int j = 0;
            for (int i = 0; i < list.size(); i++) {
                j = i<<2;
                Map.Entry<String, String> e = list.get(0);
                arr[j] = e.getKey();
                arr[j + 1] = e.getValue();
            }
            jedisCluster.mset(arr);
        }
    }

    @AfterEach
    void tearDown() {
        if (jedisCluster != null) {
            jedisCluster.close();
        }
    }
}
```

2.2.2 Spring集群环境下批处理代码

```java
   @Test
    void testMSetInCluster() {
        Map<String, String> map = new HashMap<>(3);
        map.put("name", "Rose");
        map.put("age", "21");
        map.put("sex", "Female");
        stringRedisTemplate.opsForValue().multiSet(map);


        List<String> strings = stringRedisTemplate.opsForValue().multiGet(Arrays.asList("name", "age", "sex"));
        strings.forEach(System.out::println);

    }
```

**原理分析**

在RedisAdvancedClusterAsyncCommandsImpl 类中

首先根据slotHash算出来一个partitioned的map，map中的key就是slot，而他的value就是对应的对应相同slot的key对应的数据

通过 RedisFuture\<String> mset = super.mset(op);进行异步的消息发送

```Java
@Override
public RedisFuture<String> mset(Map<K, V> map) {

    Map<Integer, List<K>> partitioned = SlotHash.partition(codec, map.keySet());

    if (partitioned.size() < 2) {
        return super.mset(map);
    }

    Map<Integer, RedisFuture<String>> executions = new HashMap<>();

    for (Map.Entry<Integer, List<K>> entry : partitioned.entrySet()) {

        Map<K, V> op = new HashMap<>();
        entry.getValue().forEach(k -> op.put(k, map.get(k)));

        RedisFuture<String> mset = super.mset(op);
        executions.put(entry.getKey(), mset);
    }

    return MultiNodeExecution.firstOfAsync(executions);
}
```

### 3、服务器端优化-持久化配置

Redis的持久化虽然可以保证数据安全，但也会带来很多额外的开销，因此持久化请遵循下列建议：

- 用来做缓存的Redis实例尽量不要开启持久化功能
- 建议关闭RDB持久化功能，使用AOF持久化
- 利用脚本定期在slave节点做RDB，实现数据备份
- 设置合理的rewrite阈值，避免频繁的bgrewrite
- 配置no-appendfsync-on-rewrite = yes，禁止在rewrite期间做aof，避免因AOF引起的阻塞
- 部署有关建议：
  - Redis实例的物理机要预留足够内存，应对fork和rewrite
  - 单个Redis实例内存上限不要太大，例如4G或8G。可以加快fork的速度、减少主从同步、数据迁移压力
  - 不要与CPU密集型应用部署在一起
  - 不要与高硬盘负载应用一起部署。例如：数据库、消息队列

### 4、服务器端优化-慢查询优化

#### 4.1 什么是慢查询

并不是很慢的查询才是慢查询，而是：在Redis执行时耗时超过某个阈值的命令，称为慢查询。

慢查询的危害：由于Redis是单线程的，所以当客户端发出指令后，他们都会进入到redis底层的queue来执行，如果此时有一些慢查询的数据，就会导致大量请求阻塞，从而引起报错，所以我们需要解决慢查询问题。

![1653129590210](./imgs/1653129590210.png)

慢查询的阈值可以通过配置指定：

slowlog-log-slower-than：慢查询阈值，单位是微秒。默认是10000，建议1000

慢查询会被放入慢查询日志中，日志的长度有上限，可以通过配置指定：

slowlog-max-len：慢查询日志（本质是一个队列）的长度。默认是128，建议1000

![1653130457771](./imgs/1653130457771.png)

修改这两个配置可以使用：config set命令：

![1653130475979](./imgs/1653130475979.png)

#### 4.2 如何查看慢查询

知道了以上内容之后，那么咱们如何去查看慢查询日志列表呢：

- slowlog len：查询慢查询日志长度
- slowlog get [n]：读取n条慢查询日志
- slowlog reset：清空慢查询列表

![1653130858066](./imgs/1653130858066.png)

### 5、服务器端优化-命令及安全配置

安全可以说是服务器端一个非常重要的话题，如果安全出现了问题，那么一旦这个漏洞被一些坏人知道了之后，并且进行攻击，那么这就会给咱们的系统带来很多的损失，所以我们这节课就来解决这个问题。

Redis会绑定在0.0.0.0:6379，这样将会将Redis服务暴露到公网上，而Redis如果没有做身份认证，会出现严重的安全漏洞. 漏洞重现方式：https://cloud.tencent.com/developer/article/1039000

为什么会出现不需要密码也能够登录呢，主要是Redis考虑到每次登录都比较麻烦，所以Redis就有一种ssh免秘钥登录的方式，生成一对公钥和私钥，私钥放在本地，公钥放在redis端，当我们登录时服务器，再登录时候，他会去解析公钥和私钥，如果没有问题，则不需要利用redis的登录也能访问，这种做法本身也很常见，但是这里有一个前提，前提就是公钥必须保存在服务器上，才行，但是Redis的漏洞在于在不登录的情况下，也能把秘钥送到Linux服务器，从而产生漏洞

漏洞出现的核心的原因有以下几点：

- Redis未设置密码
- 利用了Redis的config set命令动态修改Redis配置
- 使用了Root账号权限启动Redis

所以：如何解决呢？我们可以采用如下几种方案

为了避免这样的漏洞，这里给出一些建议：

- Redis一定要设置密码
- 禁止线上使用下面命令：keys、flushall、flushdb、config set等命令。可以利用rename-command禁用。
- bind：限制网卡，禁止外网网卡访问
- 开启防火墙
- 不要使用Root账户启动Redis
- 尽量不是有默认的端口

### 6、服务器端优化-Redis内存划分和内存配置

当Redis内存不足时，可能导致Key频繁被删除、响应时间变长、QPS不稳定等问题。当内存使用率达到90%以上时就需要我们警惕，并快速定位到内存占用的原因。

**有关碎片问题分析**

Redis底层分配并不是这个key有多大，他就会分配多大，而是有他自己的分配策略，比如8,16,20等等，假定当前key只需要10个字节，此时分配8肯定不够，那么他就会分配16个字节，多出来的6个字节就不能被使用，这就是我们常说的 碎片问题

**进程内存问题分析：**

这片内存，通常我们都可以忽略不计

**缓冲区内存问题分析：**

一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。客户端缓冲区又包括输入缓冲区和输出缓冲区两种。这部分内存占用波动较大，所以这片内存也是我们需要重点分析的内存问题。

| **内存占用** | **说明**                                                     |
| ------------ | ------------------------------------------------------------ |
| 数据内存     | 是Redis最主要的部分，存储Redis的键值信息。主要问题是BigKey问题、内存碎片问题 |
| 进程内存     | Redis主进程本身运⾏肯定需要占⽤内存，如代码、常量池等等；这部分内存⼤约⼏兆，在⼤多数⽣产环境中与Redis数据占⽤的内存相⽐可以忽略。 |
| 缓冲区内存   | 一般包括客户端缓冲区、AOF缓冲区、复制缓冲区等。客户端缓冲区又包括输入缓冲区和输出缓冲区两种。这部分内存占用波动较大，不当使用BigKey，可能导致内存溢出。 |

于是我们就需要通过一些命令，可以查看到Redis目前的内存分配状态：

- info memory：查看内存分配的情况

![1653132073570](./imgs/1653132073570.png)

- memory xxx：查看key的主要占用情况

![1653132098823](./imgs/1653132098823.png)

接下来我们看到了这些配置，最关键的缓存区内存如何定位和解决呢？

内存缓冲区常见的有三种：

- 复制缓冲区：主从复制的repl_backlog_buf，如果太小可能导致频繁的全量复制，影响性能。通过replbacklog-size来设置，默认1mb
- AOF缓冲区：AOF刷盘之前的缓存区域，AOF执行rewrite的缓冲区。无法设置容量上限
- 客户端缓冲区：分为输入缓冲区和输出缓冲区，输入缓冲区最大1G且不能设置。输出缓冲区可以设置

以上复制缓冲区和AOF缓冲区 不会有问题，最关键就是客户端缓冲区的问题

客户端缓冲区：指的就是我们发送命令时，客户端用来缓存命令的一个缓冲区，也就是我们向redis输入数据的输入端缓冲区和redis向客户端返回数据的响应缓存区，输入缓冲区最大1G且不能设置，所以这一块我们根本不用担心，如果超过了这个空间，redis会直接断开，因为本来此时此刻就代表着redis处理不过来了，我们需要担心的就是输出端缓冲区

![1653132410073](./imgs/1653132410073.png)

我们在使用redis过程中，处理大量的big value，那么会导致我们的输出结果过多，如果输出缓存区过大，会导致redis直接断开，而默认配置的情况下， 其实他是没有大小的，这就比较坑了，内存可能一下子被占满，会直接导致咱们的redis断开，所以解决方案有两个

1、设置一个大小

2、增加我们带宽的大小，避免我们出现大量数据从而直接超过了redis的承受能力

### 7、服务器端集群优化-集群还是主从

集群虽然具备高可用特性，能实现自动故障恢复，但是如果使用不当，也会存在一些问题：

- 集群完整性问题
- 集群带宽问题
- 数据倾斜问题
- 客户端性能问题
- 命令的集群兼容性问题
- lua和事务问题

**问题1、在Redis的默认配置中，如果发现任意一个插槽不可用，则整个集群都会停止对外服务：**

大家可以设想一下，如果有几个slot不能使用，那么此时整个集群都不能用了，我们在开发中，其实最重要的是可用性，所以需要把如下配置修改成no，即有slot不能使用时，我们的redis集群还是可以对外提供服务

![1653132740637](./imgs/1653132740637.png)

**问题2、集群带宽问题**

集群节点之间会不断的互相Ping来确定集群中其它节点的状态。每次Ping携带的信息至少包括：

- 插槽信息
- 集群状态信息

集群中节点越多，集群状态信息数据量也越大，10个节点的相关信息可能达到1kb，此时每次集群互通需要的带宽会非常高，这样会导致集群中大量的带宽都会被ping信息所占用，这是一个非常可怕的问题，所以我们需要去解决这样的问题

**解决途径：**

- 避免大集群，集群节点数不要太多，最好少于1000，如果业务庞大，则建立多个集群。
- 避免在单个物理机中运行太多Redis实例
- 配置合适的cluster-node-timeout值

**问题3、命令的集群兼容性问题**

有关这个问题咱们已经探讨过了，当我们使用批处理的命令时，redis要求我们的key必须落在相同的slot上，然后大量的key同时操作时，是无法完成的，所以客户端必须要对这样的数据进行处理，这些方案我们之前已经探讨过了，所以不再这个地方赘述了。

**问题4、lua和事务的问题**

lua和事务都是要保证原子性问题，如果你的key不在一个节点，那么是无法保证lua的执行和事务的特性的，所以在集群模式是没有办法执行lua和事务的

**那我们到底是集群还是主从**

单体Redis（主从Redis）已经能达到万级别的QPS，并且也具备很强的高可用特性。如果主从能满足业务需求的情况下，所以如果不是在万不得已的情况下，尽量不搭建Redis集群

------



# 四. 原理篇

## 1. Redis数据结构

### 1.1 Redis数据结构-动态字符串

我们都知道Redis中保存的Key是字符串，value往往是字符串或者字符串的集合。可见字符串是Redis中最常用的一种数据结构。

不过Redis没有直接使用C语言中的字符串，因为C语言字符串存在很多问题： 获取字符串长度的需要通过运算 非二进制安全 不可修改 Redis构建了一种新的字符串结构，称为简单动态字符串（Simple Dynamic String），简称SDS。 例如，我们执行命令：

![1653984583289](./imgs/1653984583289.png)

那么Redis将在底层创建两个SDS，其中一个是包含“name”的SDS，另一个是包含“虎哥”的SDS。

Redis是C语言实现的，其中SDS是一个结构体，源码如下：

![1653984624671](./imgs/1653984624671.png)

例如，一个包含字符串“name”的sds结构如下：

![1653984648404](./imgs/1653984648404.png)

SDS之所以叫做动态字符串，是因为它具备动态扩容的能力，例如一个内容为“hi”的SDS：

![1653984787383](./imgs/1653984787383.png)

假如我们要给SDS追加一段字符串“,Amy”，这里首先会申请新内存空间：

如果新字符串小于1M，则新空间为扩展后字符串长度的两倍+1；

如果新字符串大于1M，则新空间为扩展后字符串长度+1M+1。称为内存预分配。

![1653984822363](./imgs/1653984822363.png)

![1653984838306](./imgs/1653984838306.png)

### 1.2 Redis数据结构-intset

IntSet是Redis中set集合的一种实现方式，基于整数数组来实现，并且具备长度可变、有序等特征。 结构如下：

![1653984923322](./imgs/1653984923322.png)

其中的encoding包含三种模式，表示存储的整数大小不同：

![1653984942385](./imgs/1653984942385.png)

为了方便查找，Redis会将intset中所有的整数按照升序依次保存在contents数组中，结构如图：

![1653985149557](./imgs/1653985149557.png)

现在，数组中每个数字都在int16_t的范围内，因此采用的编码方式是INTSET_ENC_INT16，每部分占用的字节大小为： encoding：4字节 length：4字节 contents：2字节 * 3 = 6字节

![1653985197214](./imgs/1653985197214.png)

我们向该其中添加一个数字：50000，这个数字超出了int16_t的范围，intset会自动升级编码方式到合适的大小。 以当前案例来说流程如下：

- 升级编码为INTSET_ENC_INT32, 每个整数占4字节，并按照新的编码方式及元素个数扩容数组
- 倒序依次将数组中的元素拷贝到扩容后的正确位置
- 将待添加的元素放入数组末尾
- 最后，将inset的encoding属性改为INTSET_ENC_INT32，将length属性改为4

![1653985276621](./imgs/1653985276621.png)

源码如下：

![1653985304075](./imgs/1653985304075.png)

![1653985327653](./imgs/1653985327653.png)

小总结：

Intset可以看做是特殊的整数数组，具备一些特点：

- Redis会确保Intset中的元素唯一、有序
- 具备类型升级机制，可以节省内存空间
- 底层采用二分查找方式来查询

### 1.3 Redis数据结构-Dict

我们知道Redis是一个键值型（Key-Value Pair）的数据库，我们可以根据键实现快速的增删改查。而键与值的映射关系正是通过Dict来实现的。 Dict由三部分组成，分别是：哈希表（DictHashTable）、哈希节点（DictEntry）、字典（Dict）

![1653985396560](./imgs/1653985396560.png)

当我们向Dict添加键值对时，Redis首先根据key计算出hash值（h），然后利用 h & sizemask来计算元素应该存储到数组中的哪个索引位置。我们存储k1=v1，假设k1的哈希值h =1，则1&3 =1，因此k1=v1要存储到数组角标1位置。

![1653985497735](./imgs/1653985497735.png)

Dict由三部分组成，分别是：哈希表（DictHashTable）、哈希节点（DictEntry）、字典（Dict）

![1653985570612](./imgs/1653985570612.png)

![1653985586543](./imgs/1653985586543.png)

![1653985640422](./imgs/1653985640422.png)

**Dict的扩容**

Dict中的HashTable就是数组结合单向链表的实现，当集合中元素较多时，必然导致哈希冲突增多，链表过长，则查询效率会大大降低。 Dict在每次新增键值对时都会检查负载因子（LoadFactor = used/size） ，满足以下两种情况时会触发哈希表扩容： 哈希表的 LoadFactor >= 1，并且服务器没有执行 BGSAVE 或者 BGREWRITEAOF 等后台进程； 哈希表的 LoadFactor > 5 ；

![1653985716275](./imgs/1653985716275.png)

![1653985743412](./imgs/1653985743412.png)

**Dict的rehash**

不管是扩容还是收缩，必定会创建新的哈希表，导致哈希表的size和sizemask变化，而key的查询与sizemask有关。因此必须对哈希表中的每一个key重新计算索引，插入新的哈希表，这个过程称为rehash。过程是这样的：

- 计算新hash表的realeSize，值取决于当前要做的是扩容还是收缩：
  - 如果是扩容，则新size为第一个大于等于dict.ht[0].used + 1的2^n
  - 如果是收缩，则新size为第一个大于等于dict.ht[0].used的2^n （不得小于4）
- 按照新的realeSize申请内存空间，创建dictht，并赋值给dict.ht[1]
- 设置dict.rehashidx = 0，标示开始rehash
- 将dict.ht[0]中的每一个dictEntry都rehash到dict.ht[1]
- 将dict.ht[1]赋值给dict.ht[0]，给dict.ht[1]初始化为空哈希表，释放原来的dict.ht[0]的内存
- 将rehashidx赋值为-1，代表rehash结束
- 在rehash过程中，新增操作，则直接写入ht[1]，查询、修改和删除则会在dict.ht[0]和dict.ht[1]依次查找并执行。这样可以确保ht[0]的数据只减不增，随着rehash最终为空

整个过程可以描述成：

![1653985824540](./imgs/1653985824540.png)

小总结：

Dict的结构：

- 类似java的HashTable，底层是数组加链表来解决哈希冲突
- Dict包含两个哈希表，ht[0]平常用，ht[1]用来rehash

Dict的伸缩：

- 当LoadFactor大于5或者LoadFactor大于1并且没有子进程任务时，Dict扩容
- 当LoadFactor小于0.1时，Dict收缩
- 扩容大小为第一个大于等于used + 1的2^n
- 收缩大小为第一个大于等于used 的2^n
- Dict采用渐进式rehash，每次访问Dict时执行一次rehash
- rehash时ht[0]只减不增，新增操作只在ht[1]执行，其它操作在两个哈希表

### 1.4 Redis数据结构-ZipList

ZipList 是一种特殊的“双端链表” ，由一系列特殊编码的连续内存块组成。可以在任意一端进行压入/弹出操作, 并且该操作的时间复杂度为 O(1)。

![1653985987327](./imgs/1653985987327.png)

![1653986020491](./imgs/1653986020491.png)

| **属性** | **类型** | **长度** | **用途**                                                     |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| zlbytes  | uint32_t | 4 字节   | 记录整个压缩列表占用的内存字节数                             |
| zltail   | uint32_t | 4 字节   | 记录压缩列表表尾节点距离压缩列表的起始地址有多少字节，通过这个偏移量，可以确定表尾节点的地址。 |
| zllen    | uint16_t | 2 字节   | 记录了压缩列表包含的节点数量。 最大值为UINT16_MAX （65534），如果超过这个值，此处会记录为65535，但节点的真实数量需要遍历整个压缩列表才能计算得出。 |
| entry    | 列表节点 | 不定     | 压缩列表包含的各个节点，节点的长度由节点保存的内容决定。     |
| zlend    | uint8_t  | 1 字节   | 特殊值 0xFF （十进制 255 ），用于标记压缩列表的末端。        |

**ZipListEntry**

ZipList 中的Entry并不像普通链表那样记录前后节点的指针，因为记录两个指针要占用16个字节，浪费内存。而是采用了下面的结构：

![1653986055253](./imgs/1653986055253.png)

- previous_entry_length：前一节点的长度，占1个或5个字节。
  - 如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值
  - 如果前一节点的长度大于254字节，则采用5个字节来保存这个长度值，第一个字节为0xfe，后四个字节才是真实长度数据
- encoding：编码属性，记录content的数据类型（字符串还是整数）以及长度，占用1个、2个或5个字节
- contents：负责保存节点的数据，可以是字符串或整数

ZipList中所有存储长度的数值均采用小端字节序，即低位字节在前，高位字节在后。例如：数值0x1234，采用小端字节序后实际存储值为：0x3412

**Encoding编码**

ZipListEntry中的encoding编码分为字符串和整数两种： 字符串：如果encoding是以“00”、“01”或者“10”开头，则证明content是字符串

| **编码**                                             | **编码长度** | **字符串大小**      |
| ---------------------------------------------------- | ------------ | ------------------- |
| \|00pppppp\|                                         | 1 bytes      | <= 63 bytes         |
| \|01pppppp\|qqqqqqqq\|                               | 2 bytes      | <= 16383 bytes      |
| \|10000000\|qqqqqqqq\|rrrrrrrr\|ssssssss\|tttttttt\| | 5 bytes      | <= 4294967295 bytes |

例如，我们要保存字符串：“ab”和 “bc”

![1653986172002](./imgs/1653986172002.png)

ZipListEntry中的encoding编码分为字符串和整数两种：

- 整数：如果encoding是以“11”开始，则证明content是整数，且encoding固定只占用1个字节

| **编码** | **编码长度** | **整数类型**                                               |
| -------- | ------------ | ---------------------------------------------------------- |
| 11000000 | 1            | int16_t（2 bytes）                                         |
| 11010000 | 1            | int32_t（4 bytes）                                         |
| 11100000 | 1            | int64_t（8 bytes）                                         |
| 11110000 | 1            | 24位有符整数(3 bytes)                                      |
| 11111110 | 1            | 8位有符整数(1 bytes)                                       |
| 1111xxxx | 1            | 直接在xxxx位置保存数值，范围从0001~1101，减1后结果为实际值 |

![1653986282879](./imgs/1653986282879.png)

![1653986217182](./imgs/1653986217182.png)

### 1.5 Redis数据结构-ZipList的连锁更新问题

ZipList的每个Entry都包含previous_entry_length来记录上一个节点的大小，长度是1个或5个字节： 如果前一节点的长度小于254字节，则采用1个字节来保存这个长度值 如果前一节点的长度大于等于254字节，则采用5个字节来保存这个长度值，第一个字节为0xfe，后四个字节才是真实长度数据 现在，假设我们有N个连续的、长度为250~253字节之间的entry，因此entry的previous_entry_length属性用1个字节即可表示，如图所示：

![1653986328124](./imgs/1653986328124.png)

ZipList这种特殊情况下产生的连续多次空间扩展操作称之为连锁更新（Cascade Update）。新增、删除都可能导致连锁更新的发生。

**小总结：**

**ZipList特性：**

- 压缩列表的可以看做一种连续内存空间的"双向链表"
- 列表的节点之间不是通过指针连接，而是记录上一节点和本节点长度来寻址，内存占用较低
- 如果列表数据过多，导致链表过长，可能影响查询性能
- 增或删较大数据时有可能发生连续更新问题

### 1.6 Redis数据结构-QuickList

问题1：ZipList虽然节省内存，但申请内存必须是连续空间，如果内存占用较多，申请内存效率很低。怎么办？

 答：为了缓解这个问题，我们必须限制ZipList的长度和entry大小。

问题2：但是我们要存储大量数据，超出了ZipList最佳的上限该怎么办？

 答：我们可以创建多个ZipList来分片存储数据。

问题3：数据拆分后比较分散，不方便管理和查找，这多个ZipList如何建立联系？

 答：Redis在3.2版本引入了新的数据结构QuickList，它是一个双端链表，只不过链表中的每个节点都是一个ZipList。

![1653986474927](./imgs/1653986474927.png)

为了避免QuickList中的每个ZipList中entry过多，Redis提供了一个配置项：list-max-ziplist-size来限制。 如果值为正，则代表ZipList的允许的entry个数的最大值 如果值为负，则代表ZipList的最大内存大小，分5种情况：

- -1：每个ZipList的内存占用不能超过4kb
- -2：每个ZipList的内存占用不能超过8kb
- -3：每个ZipList的内存占用不能超过16kb
- -4：每个ZipList的内存占用不能超过32kb
- -5：每个ZipList的内存占用不能超过64kb

其默认值为 -2：

![1653986642777](./imgs/1653986642777.png)

以下是QuickList的和QuickListNode的结构源码：

![1653986667228](./imgs/1653986667228.png)

我们接下来用一段流程图来描述当前的这个结构

![1653986718554](./imgs/1653986718554.png)

总结：

QuickList的特点：

- 是一个节点为ZipList的双端链表
- 节点采用ZipList，解决了传统链表的内存占用问题
- 控制了ZipList大小，解决连续内存空间申请效率问题
- 中间节点可以压缩，进一步节省了内存

1.7 Redis数据结构-SkipList

SkipList（跳表）首先是链表，但与传统链表相比有几点差异： 元素按照升序排列存储 节点可能包含多个指针，指针跨度不同。

![1653986771309](./imgs/1653986771309.png)

SkipList（跳表）首先是链表，但与传统链表相比有几点差异： 元素按照升序排列存储 节点可能包含多个指针，指针跨度不同。

![1653986813240](./imgs/1653986813240.png)

SkipList（跳表）首先是链表，但与传统链表相比有几点差异： 元素按照升序排列存储 节点可能包含多个指针，指针跨度不同。

![1653986877620](./imgs/1653986877620.png)

小总结：

SkipList的特点：

- 跳跃表是一个双向链表，每个节点都包含score和ele值
- 节点按照score值排序，score值一样则按照ele字典排序
- 每个节点都可以包含多层指针，层数是1到32之间的随机数
- 不同层指针到下一个节点的跨度不同，层级越高，跨度越大
- 增删改查效率与红黑树基本一致，实现却更简单

### 1.7 Redis数据结构-RedisObject

Redis中的任意数据类型的键和值都会被封装为一个RedisObject，也叫做Redis对象，源码如下：

1、什么是redisObject： 从Redis的使用者的角度来看，⼀个Redis节点包含多个database（非cluster模式下默认是16个，cluster模式下只能是1个），而一个database维护了从key space到object space的映射关系。这个映射关系的key是string类型，⽽value可以是多种数据类型，比如： string, list, hash、set、sorted set等。我们可以看到，key的类型固定是string，而value可能的类型是多个。 ⽽从Redis内部实现的⾓度来看，database内的这个映射关系是用⼀个dict来维护的。dict的key固定用⼀种数据结构来表达就够了，这就是动态字符串sds。而value则比较复杂，为了在同⼀个dict内能够存储不同类型的value，这就需要⼀个通⽤的数据结构，这个通用的数据结构就是robj，全名是redisObject。

![1653986956618](./imgs/1653986956618.png)

Redis的编码方式

Redis中会根据存储的数据类型不同，选择不同的编码方式，共包含11种不同类型：

| **编号** | **编码方式**            | **说明**               |
| -------- | ----------------------- | ---------------------- |
| 0        | OBJ_ENCODING_RAW        | raw编码动态字符串      |
| 1        | OBJ_ENCODING_INT        | long类型的整数的字符串 |
| 2        | OBJ_ENCODING_HT         | hash表（字典dict）     |
| 3        | OBJ_ENCODING_ZIPMAP     | 已废弃                 |
| 4        | OBJ_ENCODING_LINKEDLIST | 双端链表               |
| 5        | OBJ_ENCODING_ZIPLIST    | 压缩列表               |
| 6        | OBJ_ENCODING_INTSET     | 整数集合               |
| 7        | OBJ_ENCODING_SKIPLIST   | 跳表                   |
| 8        | OBJ_ENCODING_EMBSTR     | embstr的动态字符串     |
| 9        | OBJ_ENCODING_QUICKLIST  | 快速列表               |
| 10       | OBJ_ENCODING_STREAM     | Stream流               |

五种数据结构

Redis中会根据存储的数据类型不同，选择不同的编码方式。每种数据类型的使用的编码方式如下：

| **数据类型** | **编码方式**                                       |
| ------------ | -------------------------------------------------- |
| OBJ_STRING   | int、embstr、raw                                   |
| OBJ_LIST     | LinkedList和ZipList(3.2以前)、QuickList（3.2以后） |
| OBJ_SET      | intset、HT                                         |
| OBJ_ZSET     | ZipList、HT、SkipList                              |
| OBJ_HASH     | ZipList、HT                                        |

### 1.8 Redis数据结构-String

String是Redis中最常见的数据存储类型：

其基本编码方式是RAW，基于简单动态字符串（SDS）实现，存储上限为512mb。

如果存储的SDS长度小于44字节，则会采用EMBSTR编码，此时object head与SDS是一段连续空间。申请内存时

只需要调用一次内存分配函数，效率更高。

（1）底层实现⽅式：动态字符串sds 或者 long String的内部存储结构⼀般是sds（Simple Dynamic String，可以动态扩展内存），但是如果⼀个String类型的value的值是数字，那么Redis内部会把它转成long类型来存储，从⽽减少内存的使用。

![1653987103450](./imgs/1653987103450.png)

如果存储的字符串是整数值，并且大小在LONG_MAX范围内，则会采用INT编码：直接将数据保存在RedisObject的ptr指针位置（刚好8字节），不再需要SDS了。

![1653987159575](./imgs/1653987159575.png)

![1653987172764](./imgs/1653987172764.png)

![1653987202522](./imgs/1653987202522.png)

确切地说，String在Redis中是⽤⼀个robj来表示的。

用来表示String的robj可能编码成3种内部表⽰：OBJ_ENCODING_RAW，OBJ_ENCODING_EMBSTR，OBJ_ENCODING_INT。 其中前两种编码使⽤的是sds来存储，最后⼀种OBJ_ENCODING_INT编码直接把string存成了long型。 在对string进行incr, decr等操作的时候，如果它内部是OBJ_ENCODING_INT编码，那么可以直接行加减操作；如果它内部是OBJ_ENCODING_RAW或OBJ_ENCODING_EMBSTR编码，那么Redis会先试图把sds存储的字符串转成long型，如果能转成功，再进行加减操作。对⼀个内部表示成long型的string执行append, setbit, getrange这些命令，针对的仍然是string的值（即⼗进制表示的字符串），而不是针对内部表⽰的long型进⾏操作。比如字符串”32”，如果按照字符数组来解释，它包含两个字符，它们的ASCII码分别是0x33和0x32。当我们执行命令setbit key 7 0的时候，相当于把字符0x33变成了0x32，这样字符串的值就变成了”22”。⽽如果将字符串”32”按照内部的64位long型来解释，那么它是0x0000000000000020，在这个基础上执⾏setbit位操作，结果就完全不对了。因此，在这些命令的实现中，会把long型先转成字符串再进行相应的操作。

### 1.9 Redis数据结构-List

Redis的List类型可以从首、尾操作列表中的元素：

![1653987240622](./imgs/1653987240622.png)

哪一个数据结构能满足上述特征？

- LinkedList ：普通链表，可以从双端访问，内存占用较高，内存碎片较多
- ZipList ：压缩列表，可以从双端访问，内存占用低，存储上限低
- QuickList：LinkedList + ZipList，可以从双端访问，内存占用较低，包含多个ZipList，存储上限高

Redis的List结构类似一个双端链表，可以从首、尾操作列表中的元素：

在3.2版本之前，Redis采用ZipList和LinkedList来实现List，当元素数量小于512并且元素大小小于64字节时采用ZipList编码，超过则采用LinkedList编码。

在3.2版本之后，Redis统一采用QuickList来实现List：

![1653987313461](./imgs/1653987313461.png)

### 2.0 Redis数据结构-Set结构

Set是Redis中的单列集合，满足下列特点：

- 不保证有序性
- 保证元素唯一
- 求交集、并集、差集

![1653987342550](./imgs/1653987342550.png)

可以看出，Set对查询元素的效率要求非常高，思考一下，什么样的数据结构可以满足？ HashTable，也就是Redis中的Dict，不过Dict是双列集合（可以存键、值对）

Set是Redis中的集合，不一定确保元素有序，可以满足元素唯一、查询效率要求极高。 为了查询效率和唯一性，set采用HT编码（Dict）。Dict中的key用来存储元素，value统一为null。 当存储的所有数据都是整数，并且元素数量不超过set-max-intset-entries时，Set会采用IntSet编码，以节省内存

![1653987388177](./imgs/1653987388177.png)

结构如下：

 ![1653987454403](./imgs/1653987454403.png)

### 2.1 Redis数据结构-ZSET

ZSet也就是SortedSet，其中每一个元素都需要指定一个score值和member值：

- 可以根据score值排序后
- member必须唯一
- 可以根据member查询分数

![1653992091967](./imgs/1653992091967.png)

因此，zset底层数据结构必须满足键值存储、键必须唯一、可排序这几个需求。之前学习的哪种编码结构可以满足？

- SkipList：可以排序，并且可以同时存储score和ele值（member）
- HT（Dict）：可以键值存储，并且可以根据key找value

![1653992121692](./imgs/1653992121692.png)

![1653992172526](./imgs/1653992172526.png)

当元素数量不多时，HT和SkipList的优势不明显，而且更耗内存。因此zset还会采用ZipList结构来节省内存，不过需要同时满足两个条件：

- 元素数量小于zset_max_ziplist_entries，默认值128
- 每个元素都小于zset_max_ziplist_value字节，默认值64

ziplist本身没有排序功能，而且没有键值对的概念，因此需要有zset通过编码实现：

- ZipList是连续内存，因此score和element是紧挨在一起的两个entry， element在前，score在后
- score越小越接近队首，score越大越接近队尾，按照score值升序排列

![1653992238097](./imgs/1653992238097.png)

![1653992299740](./imgs/1653992299740.png)

### 2.2  Redis数据结构-Hash

Hash结构与Redis中的Zset非常类似：

- 都是键值存储
- 都需求根据键获取值
- 键必须唯一

区别如下：

- zset的键是member，值是score；hash的键和值都是任意值
- zset要根据score排序；hash则无需排序

（1）底层实现方式：压缩列表ziplist 或者 字典dict 当Hash中数据项比较少的情况下，Hash底层才⽤压缩列表ziplist进⾏存储数据，随着数据的增加，底层的ziplist就可能会转成dict，具体配置如下：

hash-max-ziplist-entries 512

hash-max-ziplist-value 64

当满足上面两个条件其中之⼀的时候，Redis就使⽤dict字典来实现hash。 Redis的hash之所以这样设计，是因为当ziplist变得很⼤的时候，它有如下几个缺点：

- 每次插⼊或修改引发的realloc操作会有更⼤的概率造成内存拷贝，从而降低性能。
- ⼀旦发生内存拷贝，内存拷贝的成本也相应增加，因为要拷贝更⼤的⼀块数据。
- 当ziplist数据项过多的时候，在它上⾯查找指定的数据项就会性能变得很低，因为ziplist上的查找需要进行遍历。

总之，ziplist本来就设计为各个数据项挨在⼀起组成连续的内存空间，这种结构并不擅长做修改操作。⼀旦数据发⽣改动，就会引发内存realloc，可能导致内存拷贝。

hash结构如下：

![1653992339937](./imgs/1653992339937.png)

zset集合如下：

![1653992360355](./imgs/1653992360355.png)

因此，Hash底层采用的编码与Zset也基本一致，只需要把排序有关的SkipList去掉即可：

Hash结构默认采用ZipList编码，用以节省内存。 ZipList中相邻的两个entry 分别保存field和value

当数据量较大时，Hash结构会转为HT编码，也就是Dict，触发条件有两个：

- ZipList中的元素数量超过了hash-max-ziplist-entries（默认512）
- ZipList中的任意entry大小超过了hash-max-ziplist-value（默认64字节）

![1653992413406](./imgs/1653992413406.png)

## 2. Redis网络模型

### 2.1 用户空间和内核态空间

服务器大多都采用Linux系统，这里我们以Linux为例来讲解:

ubuntu和Centos 都是Linux的发行版，发行版可以看成对linux包了一层壳，任何Linux发行版，其系统内核都是Linux。我们的应用都需要通过Linux内核与硬件交互

![1653844970346](./imgs/1653844970346.png)

用户的应用，比如redis，mysql等其实是没有办法去执行访问我们操作系统的硬件的，所以我们可以通过发行版的这个壳子去访问内核，再通过内核去访问计算机硬件

![1653845147190](./imgs/1653845147190.png)

计算机硬件包括，如cpu，内存，网卡等等，内核（通过寻址空间）可以操作硬件的，但是内核需要不同设备的驱动，有了这些驱动之后，内核就可以去对计算机硬件去进行 内存管理，文件系统的管理，进程的管理等等

![1653896065386](./imgs/1653896065386.png)

我们想要用户的应用来访问，计算机就必须要通过对外暴露的一些接口，才能访问到，从而简介的实现对内核的操控，但是内核本身上来说也是一个应用，所以他本身也需要一些内存，cpu等设备资源，用户应用本身也在消耗这些资源，如果不加任何限制，用户去操作随意的去操作我们的资源，就有可能导致一些冲突，甚至有可能导致我们的系统出现无法运行的问题，因此我们需要把用户和**内核隔离开**

进程的寻址空间划分成两部分：**内核空间、用户空间**

什么是寻址空间呢？我们的应用程序也好，还是内核空间也好，都是没有办法直接去物理内存的，而是通过分配一些虚拟内存映射到物理内存中，我们的内核和应用程序去访问虚拟内存的时候，就需要一个虚拟地址，这个地址是一个无符号的整数，比如一个32位的操作系统，他的带宽就是32，他的虚拟地址就是2的32次方，也就是说他寻址的范围就是0~2的32次方， 这片寻址空间对应的就是2的32个字节，就是4GB，这个4GB，会有3个GB分给用户空间，会有1GB给内核系统

![1653896377259](./imgs/1653896377259.png)

在linux中，他们权限分成两个等级，0和3，用户空间只能执行受限的命令（Ring3），而且不能直接调用系统资源，必须通过内核提供的接口来访问内核空间可以执行特权命令（Ring0），调用一切系统资源，所以一般情况下，用户的操作是运行在用户空间，而内核运行的数据是在内核空间的，而有的情况下，一个应用程序需要去调用一些特权资源，去调用一些内核空间的操作，所以此时他俩需要在用户态和内核态之间进行切换。

比如：

Linux系统为了提高IO效率，会在用户空间和内核空间都加入缓冲区：

写数据时，要把用户缓冲数据拷贝到内核缓冲区，然后写入设备

读数据时，要从设备读取数据到内核缓冲区，然后拷贝到用户缓冲区

针对这个操作：我们的用户在写读数据时，会去向内核态申请，想要读取内核的数据，而内核数据要去等待驱动程序从硬件上读取数据，当从磁盘上加载到数据之后，内核会将数据写入到内核的缓冲区中，然后再将数据拷贝到用户态的buffer中，然后再返回给应用程序，整体而言，速度慢，就是这个原因，为了加速，我们希望read也好，还是wait for data也最好都不要等待，或者时间尽量的短。

![1653896687354](./imgs/1653896687354.png)

### 2.2.网络模型-阻塞IO

在《UNIX网络编程》一书中，总结归纳了5种IO模型：

- 阻塞IO（Blocking IO）
- 非阻塞IO（Nonblocking IO）
- IO多路复用（IO Multiplexing）
- 信号驱动IO（Signal Driven IO）
- 异步IO（Asynchronous IO）

应用程序想要去读取数据，他是无法直接去读取磁盘数据的，他需要先到内核里边去等待内核操作硬件拿到数据，这个过程就是1，是需要等待的，等到内核从磁盘上把数据加载出来之后，再把这个数据写给用户的缓存区，这个过程是2，如果是阻塞IO，那么整个过程中，用户从发起读请求开始，一直到读取到数据，都是一个阻塞状态。

![1653897115346](./imgs/1653897115346.png)

具体流程如下图：

用户去读取数据时，会去先发起recvform一个命令，去尝试从内核上加载数据，如果内核没有数据，那么用户就会等待，此时内核会去从硬件上读取数据，内核读取数据之后，会把数据拷贝到用户态，并且返回ok，整个过程，都是阻塞等待的，这就是阻塞IO

总结如下：

顾名思义，阻塞IO就是两个阶段都必须阻塞等待：

**阶段一：**

- 用户进程尝试读取数据（比如网卡数据）
- 此时数据尚未到达，内核需要等待数据
- 此时用户进程也处于阻塞状态

阶段二：

- 数据到达并拷贝到内核缓冲区，代表已就绪
- 将内核数据拷贝到用户缓冲区
- 拷贝过程中，用户进程依然阻塞等待
- 拷贝完成，用户进程解除阻塞，处理数据

可以看到，阻塞IO模型中，用户进程在两个阶段都是阻塞状态。

![1653897270074](./imgs/1653897270074.png)

### 2.3 网络模型-非阻塞IO

顾名思义，非阻塞IO的recvfrom操作会立即返回结果而不是阻塞用户进程。

阶段一：

- 用户进程尝试读取数据（比如网卡数据）
- 此时数据尚未到达，内核需要等待数据
- 返回异常给用户进程
- 用户进程拿到error后，再次尝试读取
- 循环往复，直到数据就绪

阶段二：

- 将内核数据拷贝到用户缓冲区
- 拷贝过程中，用户进程依然阻塞等待
- 拷贝完成，用户进程解除阻塞，处理数据
- 可以看到，非阻塞IO模型中，用户进程在第一个阶段是非阻塞，第二个阶段是阻塞状态。虽然是非阻塞，但性能并没有得到提高。而且忙等机制会导致CPU空转，CPU使用率暴增。

![1653897490116](./imgs/1653897490116.png)

### 2.4 网络模型-IO多路复用

无论是阻塞IO还是非阻塞IO，用户应用在一阶段都需要调用recvfrom来获取数据，差别在于无数据时的处理方案：

如果调用recvfrom时，恰好没有数据，阻塞IO会使CPU阻塞，非阻塞IO使CPU空转，都不能充分发挥CPU的作用。 如果调用recvfrom时，恰好有数据，则用户进程可以直接进入第二阶段，读取并处理数据

所以怎么看起来以上两种方式性能都不好

而在单线程情况下，只能依次处理IO事件，如果正在处理的IO事件恰好未就绪（数据不可读或不可写），线程就会被阻塞，所有IO事件都必须等待，性能自然会很差。

就比如服务员给顾客点餐，**分两步**：

- 顾客思考要吃什么（等待数据就绪）
- 顾客想好了，开始点餐（读取数据）

要提高效率有几种办法？

方案一：增加更多服务员（多线程） 方案二：不排队，谁想好了吃什么（数据就绪了），服务员就给谁点餐（用户应用就去读取数据）

那么问题来了：用户进程如何知道内核中数据是否就绪呢？

所以接下来就需要详细的来解决多路复用模型是如何知道到底怎么知道内核数据是否就绪的问题了

这个问题的解决依赖于提出的

文件描述符（File Descriptor）：简称FD，是一个从0 开始的无符号整数，用来关联Linux中的一个文件。在Linux中，一切皆文件，例如常规文件、视频、硬件设备等，当然也包括网络套接字（Socket）。

通过FD，我们的网络模型可以利用一个线程监听多个FD，并在某个FD可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。

阶段一：

- 用户进程调用select，指定要监听的FD集合
- 核监听FD对应的多个socket
- 任意一个或多个socket数据就绪则返回readable
- 此过程中用户进程阻塞

阶段二：

- 用户进程找到就绪的socket
- 依次调用recvfrom读取数据
- 内核将数据拷贝到用户空间
- 用户进程处理数据

当用户去读取数据的时候，不再去直接调用recvfrom了，而是调用select的函数，select函数会将需要监听的数据交给内核，由内核去检查这些数据是否就绪了，如果说这个数据就绪了，就会通知应用程序数据就绪，然后来读取数据，再从内核中把数据拷贝给用户态，完成数据处理，如果N多个FD一个都没处理完，此时就进行等待。

用IO复用模式，可以确保去读数据的时候，数据是一定存在的，他的效率比原来的阻塞IO和非阻塞IO性能都要高

![1653898691736](./imgs/1653898691736.png)

IO多路复用是利用单个线程来同时监听多个FD，并在某个FD可读、可写时得到通知，从而避免无效的等待，充分利用CPU资源。不过监听FD的方式、通知的方式又有多种实现，常见的有：

- select
- poll
- epoll

其中select和pool相当于是当被监听的数据准备好之后，他会把你监听的FD整个数据都发给你，你需要到整个FD中去找，哪些是处理好了的，需要通过遍历的方式，所以性能也并不是那么好

而epoll，则相当于内核准备好了之后，他会把准备好的数据，直接发给你，咱们就省去了遍历的动作。

### 2.5 网络模型-IO多路复用-select方式

select是Linux最早是由的I/O多路复用技术：

简单说，就是我们把需要处理的数据封装成FD，然后在用户态时创建一个fd的集合（这个集合的大小是要监听的那个FD的最大值+1，但是大小整体是有限制的 ），这个集合的长度大小是有限制的，同时在这个集合中，标明出来我们要控制哪些数据，

比如要监听的数据，是1,2,5三个数据，此时会执行select函数，然后将整个fd发给内核态，内核态会去遍历用户态传递过来的数据，如果发现这里边都数据都没有就绪，就休眠，直到有数据准备好时，就会被唤醒，唤醒之后，再次遍历一遍，看看谁准备好了，然后再将处理掉没有准备好的数据，最后再将这个FD集合写回到用户态中去，此时用户态就知道了，奥，有人准备好了，但是对于用户态而言，并不知道谁处理好了，所以用户态也需要去进行遍历，然后找到对应准备好数据的节点，再去发起读请求，我们会发现，这种模式下他虽然比阻塞IO和非阻塞IO好，但是依然有些麻烦的事情， 比如说频繁的传递fd集合，频繁的去遍历FD等问题

![1653900022580](./imgs/1653900022580.png)

### 2.6 网络模型-IO多路复用模型-poll模式

poll模式对select模式做了简单改进，但性能提升不明显，部分关键代码如下：

IO流程：

- 创建pollfd数组，向其中添加关注的fd信息，数组大小自定义
- 调用poll函数，将pollfd数组拷贝到内核空间，转链表存储，无上限
- 内核遍历fd，判断是否就绪
- 数据就绪或超时后，拷贝pollfd数组到用户空间，返回就绪fd数量n
- 用户进程判断n是否大于0,大于0则遍历pollfd数组，找到就绪的fd

**与select对比：**

- select模式中的fd_set大小固定为1024，而pollfd在内核中采用链表，理论上无上限
- 监听FD越多，每次遍历消耗时间也越久，性能反而会下降

![1653900721427](./imgs/1653900721427.png)

### 2.7 网络模型-IO多路复用模型-epoll函数

epoll模式是对select和poll的改进，它提供了三个函数：

第一个是：eventpoll的函数，他内部包含两个东西

一个是：

1、红黑树-> 记录的事要监听的FD

2、一个是链表->一个链表，记录的是就绪的FD

紧接着调用epoll_ctl操作，将要监听的数据添加到红黑树上去，并且给每个fd设置一个监听函数，这个函数会在fd数据就绪时触发，就是准备好了，现在就把fd把数据添加到list_head中去

3、调用epoll_wait函数

就去等待，在用户态创建一个空的events数组，当就绪之后，我们的回调函数会把数据添加到list_head中去，当调用这个函数的时候，会去检查list_head，当然这个过程需要参考配置的等待时间，可以等一定时间，也可以一直等， 如果在此过程中，检查到了list_head中有数据会将数据添加到链表中，此时将数据放入到events数组中，并且返回对应的操作的数量，用户态的此时收到响应后，从events中拿到对应准备好的数据的节点，再去调用方法去拿数据。

小总结：

select模式存在的三个问题：

- 能监听的FD最大不超过1024
- 每次select都需要把所有要监听的FD都拷贝到内核空间
- 每次都要遍历所有FD来判断就绪状态

poll模式的问题：

- poll利用链表解决了select中监听FD上限的问题，但依然要遍历所有FD，如果监听较多，性能会下降

epoll模式中如何解决这些问题的？

- 基于epoll实例中的红黑树保存要监听的FD，理论上无上限，而且增删改查效率都非常高
- 每个FD只需要执行一次epoll_ctl添加到红黑树，以后每次epol_wait无需传递任何参数，无需重复拷贝FD到内核空间
- 利用ep_poll_callback机制来监听FD状态，无需遍历所有FD，因此性能不会随监听的FD数量增多而下降

### 2.8 网络模型-epoll中的ET和LT

当FD有数据可读时，我们调用epoll_wait（或者select、poll）可以得到通知。但是事件通知的模式有两种：

- LevelTriggered：简称LT，也叫做水平触发。只要某个FD中有数据可读，每次调用epoll_wait都会得到通知。
- EdgeTriggered：简称ET，也叫做边沿触发。只有在某个FD有状态变化时，调用epoll_wait才会被通知。

举个栗子：

- 假设一个客户端socket对应的FD已经注册到了epoll实例中
- 客户端socket发送了2kb的数据
- 服务端调用epoll_wait，得到通知说FD就绪
- 服务端从FD读取了1kb数据回到步骤3（再次调用epoll_wait，形成循环）

结论

如果我们采用LT模式，因为FD中仍有1kb数据，则第⑤步依然会返回结果，并且得到通知 如果我们采用ET模式，因为第③步已经消费了FD可读事件，第⑤步FD状态没有变化，因此epoll_wait不会返回，数据无法读取，客户端响应超时。

### 2.9 网络模型-基于epoll的服务器端流程

我们来梳理一下这张图

服务器启动以后，服务端会去调用epoll_create，创建一个epoll实例，epoll实例中包含两个数据

1、红黑树（为空）：rb_root 用来去记录需要被监听的FD

2、链表（为空）：list_head，用来存放已经就绪的FD

创建好了之后，会去调用epoll_ctl函数，此函数会会将需要监听的数据添加到rb_root中去，并且对当前这些存在于红黑树的节点设置回调函数，当这些被监听的数据一旦准备完成，就会被调用，而调用的结果就是将红黑树的fd添加到list_head中去(但是此时并没有完成)

3、当第二步完成后，就会调用epoll_wait函数，这个函数会去校验是否有数据准备完毕（因为数据一旦准备就绪，就会被回调函数添加到list_head中），在等待了一段时间后(可以进行配置)，如果等够了超时时间，则返回没有数据，如果有，则进一步判断当前是什么事件，如果是建立连接时间，则调用accept() 接受客户端socket，拿到建立连接的socket，然后建立起来连接，如果是其他事件，则把数据进行写出

![1653902845082](./imgs/1653902845082.png)

### 3.0  网络模型-信号驱动

信号驱动IO是与内核建立SIGIO的信号关联并设置回调，当内核有FD就绪时，会发出SIGIO信号通知用户，期间用户应用可以执行其它业务，无需阻塞等待。

阶段一：

- 用户进程调用sigaction，注册信号处理函数
- 内核返回成功，开始监听FD
- 用户进程不阻塞等待，可以执行其它业务
- 当内核数据就绪后，回调用户进程的SIGIO处理函数

阶段二：

- 收到SIGIO回调信号
- 调用recvfrom，读取
- 内核将数据拷贝到用户空间
- 用户进程处理数据

![1653911776583](./imgs/1653911776583.png)

当有大量IO操作时，信号较多，SIGIO处理函数不能及时处理可能导致信号队列溢出，而且内核空间与用户空间的频繁信号交互性能也较低。

#### 3.0.1 异步IO

这种方式，不仅仅是用户态在试图读取数据后，不阻塞，而且当内核的数据准备完成后，也不会阻塞

他会由内核将所有数据处理完成后，由内核将数据写入到用户态中，然后才算完成，所以性能极高，不会有任何阻塞，全部都由内核完成，可以看到，异步IO模型中，用户进程在两个阶段都是非阻塞状态。

![1653911877542](./imgs/1653911877542.png)

#### 3.0.2 对比

最后用一幅图，来说明他们之间的区别

![1653912219712](./imgs/1653912219712.png)

### 3.1 网络模型-Redis是单线程的吗？为什么使用单线程

**Redis到底是单线程还是多线程？**

- 如果仅仅聊Redis的核心业务部分（命令处理），答案是单线程
- 如果是聊整个Redis，那么答案就是多线程

在Redis版本迭代过程中，在两个重要的时间节点上引入了多线程的支持：

- Redis v4.0：引入多线程异步处理一些耗时较旧的任务，例如异步删除命令unlink
- Redis v6.0：在核心网络模型中引入 多线程，进一步提高对于多核CPU的利用率

因此，对于Redis的核心网络模型，在Redis 6.0之前确实都是单线程。是利用epoll（Linux系统）这样的IO多路复用技术在事件循环中不断处理客户端情况。

**为什么Redis要选择单线程？**

- 抛开持久化不谈，Redis是纯 内存操作，执行速度非常快，它的性能瓶颈是网络延迟而不是执行速度，因此多线程并不会带来巨大的性能提升。
- 多线程会导致过多的上下文切换，带来不必要的开销
- 引入多线程会面临线程安全问题，必然要引入线程锁这样的安全手段，实现复杂度增高，而且性能也会大打折扣

### 3.2 Redis的单线程模型-Redis单线程和多线程网络模型变更

![1653982278727](./imgs/1653982278727.png)

当我们的客户端想要去连接我们服务器，会去先到IO多路复用模型去进行排队，会有一个连接应答处理器，他会去接受读请求，然后又把读请求注册到具体模型中去，此时这些建立起来的连接，如果是客户端请求处理器去进行执行命令时，他会去把数据读取出来，然后把数据放入到client中， clinet去解析当前的命令转化为redis认识的命令，接下来就开始处理这些命令，从redis中的command中找到这些命令，然后就真正的去操作对应的数据了，当数据操作完成后，会去找到命令回复处理器，再由他将数据写出。

## 3. Redis通信协议-RESP协议

Redis是一个CS架构的软件，通信一般分两步（不包括pipeline和PubSub）：

客户端（client）向服务端（server）发送一条命令

服务端解析并执行命令，返回响应结果给客户端

因此客户端发送命令的格式、服务端响应结果的格式必须有一个规范，这个规范就是通信协议。

而在Redis中采用的是RESP（Redis Serialization Protocol）协议：

Redis 1.2版本引入了RESP协议

Redis 2.0版本中成为与Redis服务端通信的标准，称为RESP2

Redis 6.0版本中，从RESP2升级到了RESP3协议，增加了更多数据类型并且支持6.0的新特性--客户端缓存

但目前，默认使用的依然是RESP2协议，也是我们要学习的协议版本（以下简称RESP）。

在RESP中，通过首字节的字符来区分不同数据类型，常用的数据类型包括5种：

单行字符串：首字节是 ‘+’ ，后面跟上单行字符串，以CRLF（ "\r\n" ）结尾。例如返回"OK"： "+OK\r\n"

错误（Errors）：首字节是 ‘-’ ，与单行字符串格式一样，只是字符串是异常信息，例如："-Error message\r\n"

数值：首字节是 ‘:’ ，后面跟上数字格式的字符串，以CRLF结尾。例如：":10\r\n"

多行字符串：首字节是 ‘$’ ，表示二进制安全的字符串，最大支持512MB：

如果大小为0，则代表空字符串："$0\r\n\r\n"

如果大小为-1，则代表不存在："$-1\r\n"

数组：首字节是 ‘*’，后面跟上数组元素个数，再跟上元素，元素数据类型不限:

![1653982993020](./imgs/1653982993020.png)

### 3.1 Redis通信协议-基于Socket自定义Redis的客户端

Redis支持TCP通信，因此我们可以使用Socket来模拟客户端，与Redis服务端建立连接：

```java
public class Main {

    static Socket s;
    static PrintWriter writer;
    static BufferedReader reader;

    public static void main(String[] args) {
        try {
            // 1.建立连接
            String host = "192.168.150.101";
            int port = 6379;
            s = new Socket(host, port);
            // 2.获取输出流、输入流
            writer = new PrintWriter(new OutputStreamWriter(s.getOutputStream(), StandardCharsets.UTF_8));
            reader = new BufferedReader(new InputStreamReader(s.getInputStream(), StandardCharsets.UTF_8));

            // 3.发出请求
            // 3.1.获取授权 auth 123321
            sendRequest("auth", "123321");
            Object obj = handleResponse();
            System.out.println("obj = " + obj);

            // 3.2.set name 虎哥
            sendRequest("set", "name", "虎哥");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);

            // 3.2.set name 虎哥
            sendRequest("get", "name");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);

            // 3.2.set name 虎哥
            sendRequest("mget", "name", "num", "msg");
            // 4.解析响应
            obj = handleResponse();
            System.out.println("obj = " + obj);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            // 5.释放连接
            try {
                if (reader != null) reader.close();
                if (writer != null) writer.close();
                if (s != null) s.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    private static Object handleResponse() throws IOException {
        // 读取首字节
        int prefix = reader.read();
        // 判断数据类型标示
        switch (prefix) {
            case '+': // 单行字符串，直接读一行
                return reader.readLine();
            case '-': // 异常，也读一行
                throw new RuntimeException(reader.readLine());
            case ':': // 数字
                return Long.parseLong(reader.readLine());
            case '$': // 多行字符串
                // 先读长度
                int len = Integer.parseInt(reader.readLine());
                if (len == -1) {
                    return null;
                }
                if (len == 0) {
                    return "";
                }
                // 再读数据,读len个字节。我们假设没有特殊字符，所以读一行（简化）
                return reader.readLine();
            case '*':
                return readBulkString();
            default:
                throw new RuntimeException("错误的数据格式！");
        }
    }

    private static Object readBulkString() throws IOException {
        // 获取数组大小
        int len = Integer.parseInt(reader.readLine());
        if (len <= 0) {
            return null;
        }
        // 定义集合，接收多个元素
        List<Object> list = new ArrayList<>(len);
        // 遍历，依次读取每个元素
        for (int i = 0; i < len; i++) {
            list.add(handleResponse());
        }
        return list;
    }

    // set name 虎哥
    private static void sendRequest(String ... args) {
        writer.println("*" + args.length);
        for (String arg : args) {
            writer.println("$" + arg.getBytes(StandardCharsets.UTF_8).length);
            writer.println(arg);
        }
        writer.flush();
    }
}
```

## 4. Redis内存回收-过期key处理

Redis之所以性能强，最主要的原因就是基于内存存储。然而单节点的Redis其内存大小不宜过大，会影响持久化或主从同步性能。 我们可以通过修改配置文件来设置Redis的最大内存：

![1653983341150](./imgs/1653983341150.png)

当内存使用达到上限时，就无法存储更多数据了。为了解决这个问题，Redis提供了一些策略实现内存回收：

内存过期策略

在学习Redis缓存的时候我们说过，可以通过expire命令给Redis的key设置TTL（存活时间）：

![1653983366243](./imgs/1653983366243.png)

可以发现，当key的TTL到期以后，再次访问name返回的是nil，说明这个key已经不存在了，对应的内存也得到释放。从而起到内存回收的目的。

Redis本身是一个典型的key-value内存存储数据库，因此所有的key、value都保存在之前学习过的Dict结构中。不过在其database结构体中，有两个Dict：一个用来记录key-value；另一个用来记录key-TTL。

![1653983423128](./imgs/1653983423128.png)

![1653983606531](./imgs/1653983606531.png)

这里有两个问题需要我们思考： Redis是如何知道一个key是否过期呢？

利用两个Dict分别记录key-value对及key-ttl对

是不是TTL到期就立即删除了呢？

**惰性删除**

惰性删除：顾明思议并不是在TTL到期后就立刻删除，而是在访问一个key的时候，检查该key的存活时间，如果已经过期才执行删除。

![1653983652865](./imgs/1653983652865.png)

**周期删除**

周期删除：顾明思议是通过一个定时任务，周期性的抽样部分过期的key，然后执行删除。执行周期有两种： Redis服务初始化函数initServer()中设置定时任务，按照server.hz的频率来执行过期key清理，模式为SLOW Redis的每个事件循环前会调用beforeSleep()函数，执行过期key清理，模式为FAST

周期删除：顾明思议是通过一个定时任务，周期性的抽样部分过期的key，然后执行删除。执行周期有两种： Redis服务初始化函数initServer()中设置定时任务，按照server.hz的频率来执行过期key清理，模式为SLOW Redis的每个事件循环前会调用beforeSleep()函数，执行过期key清理，模式为FAST

SLOW模式规则：

- 执行频率受server.hz影响，默认为10，即每秒执行10次，每个执行周期100ms。
- 执行清理耗时不超过一次执行周期的25%.默认slow模式耗时不超过25ms
- 逐个遍历db，逐个遍历db中的bucket，抽取20个key判断是否过期
- 如果没达到时间上限（25ms）并且过期key比例大于10%，再进行一次抽样，否则结束
- FAST模式规则（过期key比例小于10%不执行 ）：
- 执行频率受beforeSleep()调用频率影响，但两次FAST模式间隔不低于2ms
- 执行清理耗时不超过1ms
- 逐个遍历db，逐个遍历db中的bucket，抽取20个key判断是否过期 如果没达到时间上限（1ms）并且过期key比例大于10%，再进行一次抽样，否则结束

小总结：

RedisKey的TTL记录方式：

在RedisDB中通过一个Dict记录每个Key的TTL时间

过期key的删除策略：

惰性清理：每次查找key时判断是否过期，如果过期则删除

定期清理：定期抽样部分key，判断是否过期，如果过期则删除。 定期清理的两种模式：

SLOW模式执行频率默认为10，每次不超过25ms

FAST模式执行频率不固定，但两次间隔不低于2ms，每次耗时不超过1ms

### 4.1 Redis内存回收-内存淘汰策略

内存淘汰：就是当Redis内存使用达到设置的上限时，主动挑选部分key删除以释放更多内存的流程。Redis会在处理客户端命令的方法processCommand()中尝试做内存淘汰：

![1653983978671](./imgs/1653983978671.png)

淘汰策略

Redis支持8种不同策略来选择要删除的key：

- noeviction： 不淘汰任何key，但是内存满时不允许写入新数据，默认就是这种策略。
- volatile-ttl： 对设置了TTL的key，比较key的剩余TTL值，TTL越小越先被淘汰
- allkeys-random：对全体key ，随机进行淘汰。也就是直接从db->dict中随机挑选
- volatile-random：对设置了TTL的key ，随机进行淘汰。也就是从db->expires中随机挑选。
- allkeys-lru： 对全体key，基于LRU算法进行淘汰
- volatile-lru： 对设置了TTL的key，基于LRU算法进行淘汰
- allkeys-lfu： 对全体key，基于LFU算法进行淘汰
- volatile-lfu： 对设置了TTL的key，基于LFI算法进行淘汰 比较容易混淆的有两个：
  - LRU（Least Recently Used），最少最近使用。用当前时间减去最后一次访问时间，这个值越大则淘汰优先级越高。
  - LFU（Least Frequently Used），最少频率使用。会统计每个key的访问频率，值越小淘汰优先级越高。

Redis的数据都会被封装为RedisObject结构：

![1653984029506](./imgs/1653984029506.png)

LFU的访问次数之所以叫做逻辑访问次数，是因为并不是每次key被访问都计数，而是通过运算：

- 生成0~1之间的随机数R
- 计算 (旧次数 * lfu_log_factor + 1)，记录为P
- 如果 R < P ，则计数器 + 1，且最大不超过255
- 访问次数会随时间衰减，距离上一次访问时间每隔 lfu_decay_time 分钟，计数器 -1

最后用一副图来描述当前的这个流程吧

![1653984085095](./imgs/1653984085095.png)

------

