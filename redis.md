官网： https://docs.spring.io/spring-data/redis/docs/2.0.3.RELEASE/reference/html/#redis:connectors:connection

翻译后的：
https://www.cnblogs.com/spihz/p/8548800.html
http://www.redis.cn/topics/cluster-tutorial.html


要缓存的 Java 对象必须实现 Serializable 接口，因为 Spring 会将对象先序列化再存入 Redis


## Redis序列化常见的方式：
JdkSerializationRedisSerializer：JDK自带的序列化方式、存储的字符串内容在序列化的情况下偏长，会占用过多的内存

OxmSerializer：序列化的时间相对较长

Jackson2JsonRedisSerializer：json数据格式、序列化时间和序列化之后内容的长度都要优于前两种，对比前者我选择的序列化方式是第三种
9

## NoSQL
NoSQL(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指非关系型的数据库。

为什么需要 NoSQL ？
随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题。 -- 百度百科

NoSQL 数据库的四大分类
- 键值（key-value）存储
- 列存储
- 文档数据库
- 图形数据库

NoSQL 的特点
- 易扩展
- 灵活的数据模型
- 大数据量，高性能
- 高可用


|  分类   | 相关产品  | 典型应用  | 数据模型  | 优点  | 缺点  
|  ----  | ----  |
| 键值（key-value)  | Tokyo、 Cabinet/Tyrant、Redis、Voldemort、Berkeley DB | 内容缓存，主要用于处理大量数据的高访问负载 |一系列键值对	| 快速查询 | 存储的数据缺少结构化
| 列存储数据库 | 	Cassandra, HBase, Riak | 分布式的文件系统	| 以列簇式存储，将同一列数据存在一起 | 查找速度快，可扩展性强，更容易进行分布式扩展	 | 功能相对局限
| 文档数据库 |	CouchDB, MongoDB | Web应用（与Key-Value类似，value是结构化的）|	一系列键值对	| 数据结构要求不严格 | 查询性能不高，而且缺乏统一的查询语法
| 图形（Graph）| 数据库Neo4J, InfoGrid, Infinite Graph |	社交网络，推荐系统等。专注于构建关系图谱	| 图结构	 |利用图结构相关算法 | 需要对整个图做计算才能得出结果，不容易做分布式集群方案

Redis出现的原因：



Redis的应用场景
- 


- 任务队列
- 网站访问统计
- 应用排行榜
- 数据过期处理
- 分布式集群架构中的 session 分离

##Redis（远程字典服务器）简介
Redis（REmote DIctionary Server）是一个Key Value存储系统
https://www.cnblogs.com/EasonJim/p/7803067.html
引用官网的简介，Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API

Redis is an in-memory database that persists on disk. The data model is key-value, but many different kind of values are supported: Strings, Lists, Sets, Sorted Sets, Hashes, HyperLogLogs, Bitmaps.

redis是使用C语言编写的，但是string数据类型并没有使用C语言的字符串，而是重新编写一个简单的动态字符串（simple dynamic string,SDS


## 关键优势
Redis 的优势包括它的速度、它对富数据类型的支持、它的操作的原子性，以及它的通用性：

Redis 非常快。它每秒可执行约 100,000 个 SET 以及约 100,000 个 GET 操作。您可以使用 redis-benchmark 实用程序在自己的机器上对它的性能进行基准测试。（redis-benchmark 模拟在它发送总共 M 个查询的同时，N 个客户端完成的 SET/GET 操作。）
Redis 对大多数开发人员已知道的大多数数据类型提供了原生支持，这使得各种问题得以轻松解决。经验会告诉您哪个问题最好由何种数据类型来处理。
因为所有 Redis 操作都是原子性的，所以多个客户端会并发地访问一个 Redis 服务器，获取相同的更新值。
Redis 是一个多效用工具，对许多用例很有用，这些用例包括缓存、消息队列（Redis 原生支持发布/订阅）、短期应用程序数据（比如 Web 会话、Web 页面命中计数）等。

## Redis 数据类型示例
https://www.ibm.com/developerworks/cn/java/os-springredis/index.html


## RedisTemplate
使用 Spring Data Redis，Java 开发人员可以编程方式访问 Redis 并执行相应操作。Spring 框架总是推荐一种基于 POJO（plain old Java object，简单 Java 对象）的编程模型，高度重视生产力、一致性和可移植性。这些值会传输到 Spring Data Redis 项目

Spring Data Redis 在现有 Redis 客户端库（比如 Jedis、JRedis、redis-protocol 和 RJC，参见 参考资料）上提供了一种抽象。通过消除与 Redis 交互所需的样板代码，它使得使用 Redis 键-值数据存储变得很容易，无需了解低级 Redis API。它还提供了一个名为 RedisTemplate 的泛化的模板类（类似于 JDBCTemplate 或 HibernateTemplate）来与 Redis 进行交互。RedisTemplate 是与 Redis 执行面向对象的交互的主要类。它处理对象序列化和类型转换，使得作为开发人员的您在处理对象时无需担忧序列化和数据转换

## RedisConnectionFactory 和 RedisTemplate
RedisConnectionFactory 是一个用来与 Redis 建立连接的线程安全的连接工厂，RedisConnection 是连接到 Redis 的一个短期、非线程安全的连接。RedisConnection 提供了与 Redis 命令的一对一映射，而 RedisConnectionFactory 提供了有助于消除样板代码的便捷方法。RedisConnectionFactory 使不同 Redis 客户端 API 之间的切换就像定义一个 bean 那么简单。我们将对样例应用程序使用 JedisConnectionFactory，但也可使用其他任何 ConnectionFactory 变体

SpringRedisTemplate 是 RedisTemplate 的一个处理字符串的特殊版本。

RedisTemplate 提供了键类型操作，比如 ValueOperations、ListOperations、SetOperations、HashOperations 和 ZSetOperations


## Redis数据结构简介
存储 键值对 
值：5种不同数据结构类型之间的映射，数据结构类型分别为String（字符串）、List（列表）、Set（集合）、Hash（散列）和 Zset（有序集合）

|  结构类型   | 结构存储的值  | 结构的读写能力  |
|  ----  | ----  |
| String  | 可以是字符串、整数或者浮点数 |对整个字符串或者字符串的其中一部分执行操作；对象和浮点数执行自增(increment)或者自减(decrement)
| List  | 一个链表，链表上的每个节点都包含了一个字符串 | 从链表的两端推入或者弹出元素；根据偏移量对链表进行修剪(trim)；读取单个或者多个元素；根据值来查找或者移除元素
| Set  | 包含字符串的无序收集器(unorderedcollection)，并且被包含的每个字符串都是独一无二的、各不相同 | 添加、获取、移除单个元素；检查一个元素是否存在于某个集合中；计算交集、并集、差集；从集合里面随机获取元素
| Hash  | 包含键值对的无序散列表 | 添加、获取、移除单个键值对；获取所有键值对
| Zset  | 字符串成员(member)与浮点数分值(score)之间的有序映射，元素的排列顺序由分值的大小决定 | 添加、获取、删除单个元素；根据分值范围(range)或者成员来获取元素

## RedisTemplate介绍

这个主要是根据redis存储的数据类型需求决定，key一般都是String，但是value可能不一样，一般有两种，String和 Object； 
如果k-v都是String类型，我们可以直接用 StringRedisTemplate，这个是官方建议的，也是最方便的，直接导入即用，无需多余配置！ 
如果k-v是Object类型，则需要自定义 RedisTemplate


Spring封装了RedisTemplate对象来进行对Redis的各种操作，它支持所有的Redis原生的api。RedisTemplate位于spring-data-redis包下

RedisTemplate<K,V>

K 类型说明：
K 类型只能是String,即是RedisTemplate<String,Object>
the Redis key type against which the template works (usually a String)
若定义为RedisTemplate<Object, Object>，否则根据里氏替换原则，使用的时候会造成类型错误
里氏替换原则？？

V 类型说明：
```
redisTemplate.opsForValue();//操作字符串
redisTemplate.opsForHash();//操作hash
redisTemplate.opsForList();//操作list
redisTemplate.opsForSet();//操作set
redisTemplate.opsForZSet();//操作有序set
```
## StringRedisTemplate与RedisTemplate的关系
RedisTemplate看这个类的名字后缀是Template，如果了解过Spring如何连接关系型数据库的，大概不会难猜出这个类是做什么的 ，它跟JdbcTemplate一样封装了对Redis的一些常用的操作，当然StringRedisTemplate跟RedisTemplate功能类似那么肯定就会有人问，为什么会需要两个Template呢，一个不就够了吗？其实他们两者之间的区别主要在于他们使用的序列化类。

RedisTemplate使用的序列类在在操作数据的时候，比如说存入数据会将数据先序列化成字节数组
然后在存入Redis数据库，这个时候打开Redis查看的时候，你会看到你的数据不是以可读的形式
展现的，而是以字节数组显示


**StringRedisTemplate是Spring Boot内置的操作Redis的API实现类,另外还有一个API实现类是RedisTemplate。
StringRedisTemplate的API假定所有的数据类型化都是字符类型，即key和value都是字符串类型，对于常见额SpringBoot应用系统，使用字符串操作也已经足够了，而且能方便的通过客户端管理工具管理**

spring-boot-autoconfigure-2.0.4.RELEASE.jar包中RedisAutoConfiguration.java已经自动声明了两个redis操作bean

可以发现，出现了一些无法识别的字符，查看RedisTemplate源码可这个是由于默认使用了JDK的序列化机制，而StringRedisTemplate没有出乱码是因为它修改了序列化器


- RedisTemplate是一个泛型类，StringRedisTemplate继承RedisTemplate
- 两者的数据是不共通的；也就是说StringRedisTemplate只能管理StringRedisTemplate里面的数据，RedisTemplate只能管理RedisTemplate中的数据
- RedisTemplate默认使用的是JdkSerializationRedisSerializer，StringRedisTemplate默认使用的是StringRedisSerializer
- 他们各自序列化的方式不同，但最终都是得到了一个字节数组

RedisTemplate默认采用的是JDK的序列化策略，保存的key和value都是采用此策略序列化保存的

当我们的数据存储到Redis的时候，我们的键（key）和值（value）都是通过Spring提供的Serializer序列化到数据库的。


注意:
- 使用RedisTemplate需要更改序列化方式  RedisConfig
- 如果存储的是其他类型，比如对象，集合这些，就要用RedisTemplate


使用时注意事项：
　　　当你的redis数据库里面本来存的是字符串数据或者你要存取的数据就是字符串类型数据的时候，那么你就使用StringRedisTemplate即可。
　　　但是如果你的数据是复杂的对象类型，而取出的时候又不想做任何的数据转换，直接从Redis里面取出一个对象，那么使用RedisTemplate是更好的选择,需要自定义序列器。
　　　**redisTemplate 中存取数据都是字节数组**。当redis中存入的数据是可读形式而非字节数组时，使用redisTemplate取值的时候会无法获取导出数据，获得的值为null，需要自定义序列器。



spring-data-redis的RedisTemplate<K, V>模板类在操作redis时默认使用JdkSerializationRedisSerializer来进行序列化：

JdkSerializationRedisSerializer
SerializingConverter
DefaultSerializer
Serializer
OutputStream 编码是ISO-8859-1

解决方案：   设置序列器
StringRedisSerializer 
```
public StringRedisSerializer() {
        this(StandardCharsets.UTF_8);
    }
```



```
\xAC\xED\x00\x05t\x00\x05hello
```


RedisTemplate中定义了5种数据结构操作

```
redisTemplate.opsForValue();　　//操作字符串
redisTemplate.opsForHash();　　 //操作hash
redisTemplate.opsForList();　　 //操作list
redisTemplate.opsForSet();　　  //操作set
redisTemplate.opsForZSet();　 　//操作有序set
```



## ReactiveRedisTemplate  ?? 

project: reactivertringredisremplate

- 1.引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
```


## Redis数据结构简介



RedisTemplate配置：
```
@Bean
public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory)
{
        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
        template.setConnectionFactory(redisConnectionFactory);
        template.setKeySerializer(jackson2JsonRedisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashKeySerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
}
```




## Redis的几种数据结构的实现具体是什么 ？？
## ZSetOperations.TypedTuple  接口中的接口 ？？

## 读取RDB文件 ->  二进制文件
计算机内部都是使用二进制保存文件的，但是这种方式程序员阅读起来比较累，因为即便一个数字也得写上一大串。所以我们用16进制来表示二进制，每4个二进制对应一个16进制，每个字节用2个16进制字符就能表示了

## 持久化 - AOF 和 RDB
Redis是内存数据库，所以持久化是必须的，redis提供 RDB 和 AOF 两种持久化方案。

RDB 即 Redis DataBase，文件中存的是数据
AOF 即 Append Only File，文件中存的是写操作命令


Redis进行操作时，有两种方式将操作的结果保存下来。一种是将结果以快照的方式保存在二进制文件中（默认叫：dump.rdb* ），这就是RDB模式，另一种是将操作的命令追加到一个记录文件里（默认叫：appendonly.aof），然后通过重新执行这些命令来重建数据库

在redis里，默认使用RDB模式。因为RDB模式重建数据库比较快

### RDB模式


![avatar](E:\markdown笔记\images\RDB.png.png)

重建数据库 是指将数据从硬盘移到内存，并建立起数据库的过程。对于RDB模式来说，就是把 dump.rdb 文件加载到内存，并解压字符串，就建立起了数据库。而对于AOF模式来说，则是在启动Redis服务器的时候，运行appendonly.aof日志文件，在内存中重新建立数据库。从这里的描述就可以看出，AOF的重建过程是要比RDB慢的

使用RDB模式的话，系统会将内存中数据库的快照每隔一段时间间隔更新到硬盘中（dump.rdb 文件里），这个更新的频率是可以指定的。在redis.conf(redis.windows.conf)中有三个配置用来指定内存数据更新到硬盘的频率：

//格式是：save <seconds> <changes>
save 900 1           //如果仅有1-9次更改操作，那么要900s才写入硬盘一次
save 300 10          //如果仅有10-9999次更改操作，那么要300s才写入硬盘一次
save 60 10000        //如果超过10000次更改操作，那么60s才会写入硬盘一次

900s，也就是15分钟，300s，就是5分钟。这个时间挺长的，这正是RDB模式的缺点所在——如果服务器宕机的话，可能会造成最后几分钟，保存在内存中还来不及刷入硬盘的数据丢失。如果数据很重要那就惨了。

但是，如果数据不是那么重要，丢失几分钟数据也没什么关系，那么RDB模式是最好的选择

### AOF模式
redis目录下默认没有生成appendonly.aof文件
要生成appendonly.aof文件，首先要将redis的RDB方式切换为AOF方式
- 首先修改redis.conf配置文件中的appendonly参数，改为yes
- 重启redis服务，如果还没有生成appendonly.aof，就再执行以下两行命令（注：想要生成appendonly.aof文件，必须要执行redis命令才会生成对应的命令记录，aof文件其实就是记录你执行过的redis命令）

```
redis-cli config set appendonly yes
redis-cli config set save ""  // 用于关闭 RDB 功能。 这一步是可选的， 如果你愿意的话， 也可以同时使用 RDB 和 AOF 这两种持久化功能
```
服务器上面就生成了对应的appendonly.aof，一般这个文件主要是用来恢复redis数据库的


**若同时开启RDB模式和AOF模式 从appendonly.aof恢复数据**


Redis 默认开启RDB，在指定的时间间隔内，执行指定次数的写操作，则将内存中的数据写入到磁盘中。
RDB 持久化适合大规模的数据恢复，但数据一致性和完整性较差。
Redis 可手动开启AOF，默认是每秒将写操作日志追加到AOF文件中。
AOF 的数据完整性比RDB高，但文件会越来越大，影响数据恢复效率，针对 AOF文件大问题，提供AOF重写机制（将多个命令操作同一数据的合并为一个命令）。
Redis 做缓存使用时，可关闭持久化。
若使用Redis 的持久化，建议RDB和AOF都开启。
RDB，AOF同时存在时，优先使用AOF文件

附：
#### appendonly.aof文件说明：
*N 是命令的参数个数，命令本身也是参数
$M 是参数的长度，即字节数

```
*2 			#当前命令有两个参数
$6 			#第一个参数长度是6字节，即SELECT
SELECT 		#当前命令的第一个参数值
$1 			#当前命令的第二个参数，长度是1，即0
0 			#当前命令的第二个参数值
```
#### dump.rdb文件说明：

Hex-editor  NotePadd++ 二进制查看插件
文件解析 ？？？

RDB结构剖析：od –cx dump.rdb
https://www.cnblogs.com/huangxincheng/p/5074998.html

Redis的RDB文件是二进制格式的文件，从这个方面再次体现了Redis是基于内存的缓存数据库，不管对于存储到硬盘还是恢复数据都十分快捷。Redis有多种数据类型：string、list、hash、set、zset，不同数据类型占用的内存大小是不一样的，解析出自然语言可以识别的数据就需要使用不同的方法，保存到文件的时候也需要一些协议或者规则。这有点类似于编程语言里面的数据类型，不同的数据类型占用的字节大小不一致，但是保存到计算机都是二进制的编码，就看是读取多少个字节，以怎样的方式解读。

举个例子，redis的对象类型是特定的几个字符表示，0代表字符串，读取到字符串类型后，紧接着就是字符串的长度，保存着接下来需要读取的字节大小，读取到的字节最终构成完整字符串对象的值。对于保存了"name" => "hoohack"键值对的字符串对象保存到内存可以用下图表示：

![avatar](E:\markdown笔记\images\redis_hoohack.png)


redis字符串存储

当然，除了字符串，redis还有列表，集合，哈希等各种对象，针对这些类型，在RDB文件里面都有不同的规则定义，只需要按照RDB文件格式的协议来解读文件，就能完整无误地把文件解读成自然语言能描述的字符。


#### redis关闭持久化：做缓存
禁用Redis服务器的持久化功能，这样我们就可以将Redis视为一个功能加强版的memcached了。
无持久化的配置，将RDB配置全部注释掉

修改配置文件:
- 1. 注释掉原来的持久化规则
```
#save 900 1
#save 300 10
#save 60 10000
```
- 2. appendonly 仍旧为no，同时设置 save ""
- 3. 重启

执行操作命令: 无需重启

```
CONFIG SET save ""
```


## redis 数据结构使用场景
https://www.jb51.net/article/54774.htm
https://blog.csdn.net/hguisu/article/details/8836819


## redis 数据库

### 持久化存储
redis是默认开启了持久化的
会存在rdb.dump文件里
启动的时候，就从这个文件读出来

- 



听，，，，:
但是取出来的，都是object的

听，，，，:
用stringredistemplate 取出来的就是string的，默认序列化了的

 

## 使用Redis
spring-boot-starter-data-redis 已经包含了jedis客户端，我们在使用jedis连接池的时候不必再添加jedis依赖。
### 第一步:引入依赖包
```
<!-- redis客户端 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```
### 第二步：配置
```

```
### 第三步：使用redisTemplate工具类
```
redisTemplate.opsForValue().set(key, value)
redisTemplate.opsForValue().get(key)
redisTemplate.delete("1")
```


## Spring Data ReactiveRedis
## Spring Data Redis
它向Redis提供Spring Data平台的抽象- Redis是流行的内存数据结构存储。
Redis由基于密钥库的数据结构驱动，以持久保存数据，并可用作数据库，缓存，消息代理等
- Maven依赖
- Redis配置
	- Java配置，定义bean
	- 自定义连接属性 jedisConnectionFactory配置
	- 

JedisConnectionFacotory从Spring Data Redis 2.0开始已经不推荐直接显示设置连接的信息了，一方面为了使配置信息与建立连接工厂解耦，另一方面抽象出Standalone,Sentinel和RedisCluster三种模式的环境配置类和一个统一的jedis客户端连接配置类（用于配置连接池和SSL连接），使得我们可以更加灵活方便根据实际业务场景需要来配置连接信息

## spring-data-redis

project： redis-client

它依赖jedis或Lettuce，实际上是对jedis这些客户端的封装，提供一套与客户端无关的api供应用使用，从而你在从一个redis客户端切换为另一个客户端，不需要修改业务代码


在 springboot 1.5.x版本的默认的Redis客户端是 Jedis实现的，**springboot 2.x版本中默认客户端是用 lettuce实现的**


### spring boot 整合data redis（默认依赖Lettuce）

- 1.引入依赖 pom.xml

```
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
 </dependency>
```

- 2.application.yml

redis配置项查看：
org.springframework.boot.autoconfigure.data.redis.RedisProperties

```
##redis 最基本配置
spring:
  #redis配置
  redis:
    # Redis数据库索引（默认为0）
    database: 0
    host: 127.0.0.1
    port: 6379
    password: zhangjun
```

### spring boot 整合data redis （jedis）

- 1.引入依赖 pom.xml

```
<!-- spring-boot-starter-data-redis默认只引入了 Lettuce包，并没有引入 jedis包支持。需要手动引入 jedis的包，并排除掉 lettuce的包-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <!-- 排除lettuce包，使用jedis代替-->
            <exclusions>
                <exclusion>
                    <groupId>io.lettuce</groupId>
                    <artifactId>lettuce-core</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- jedis -->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
```

- 2.application.yml

```
##redis 最基本配置
spring:
  #redis配置
  redis:
    # Redis数据库索引（默认为0）
    database: 0
    host: 127.0.0.1
    port: 6379
    password: zhangjun
```



## Redis 客户端
https://redis.io/clients#java

功能：
java客户端，通过它可以对redis进行操作

### Jedis vs Lettuce

Jedis和Lettuce都是Redis Client


比较突出的是 Lettuce 和 jedis。Lettuce 和 jedis 的都是连接 Redis Server的客户端，Jedis 在实现上是直连 redis server，多线程环境下非线程安全，除非使用连接池，为每个 redis实例增加 物理连接。


Jedis 是直连模式，在多个线程间共享一个 Jedis 实例时是线程不安全的，
如果想要在多线程环境下使用 Jedis，需要使用连接池，每个线程都去拿自己的 Jedis 实例，当连接数量增多时，物理连接成本就较高了。


![avatar](E:\markdown笔记\images\Jedis直连Redis.png)
![avatar](E:\markdown笔记\images\Jedis连接池使用方式.png)


Lettuce的连接是基于Netty的，连接实例可以在多个线程间共享，所以，一个多线程的应用可以使用同一个连接实例，而不用担心并发线程的数量。当然这个也是可伸缩的设计，一个连接实例不够的情况也可以按需增加连接实例。

通过异步的方式可以让我们更好的利用系统资源，而不用浪费线程等待网络或磁盘I/O。Lettuce 是基于 netty 的，netty 是一个多线程、事件驱动的 I/O 框架，所以 Lettuce 可以帮助我们充分利用异步的优势。



lettuce
Lettuce is a scalable thread-safe Redis client for synchronous, asynchronous and reactive usage. Multiple threads may share one connection if they avoid blocking and transactional operations such as BLPOP and MULTI/EXEC. Lettuce is built with netty. Supports advanced Redis features such as Sentinel, Cluster, Pipelining, Auto-Reconnect and Redis data models



We've covered Jedis in one of the previous posts. What makes Lettuce different?

The most significant difference is its asynchronous support via the Java 8's CompletionStage interface and support for Reactive Streams. As we'll see below, Lettuce offers a natural interface for making asynchronous requests from the Redis database server and for creating streams.

It also uses Netty for communicating with the server. This makes for a “heavier” API, but also makes it better suited for sharing a connection with more than one thread.



Lettuce 是 一种可伸缩，线程安全，完全非阻塞的Redis客户端，多个线程可以共享一个RedisConnection,它利用Netty NIO 框架来高效地管理多个连接，从而提供了异步和同步数据访问方式，用于构建非阻塞的反应性应用程序。



### 连接池配置
project: redis-client

1. 无需每次连接生成Jedis对象，降低开销
2. 使用连接池的形式保护和控制资源的使用

使用apache commons-pool2对Jedis资源池进行管理，使用redis连接池（无论lettuce还是jedis客户端，都需要），则需要导入如下依赖

```
<dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-pool2</artifactId>
 </dependency>
```
connectionFactory -> connectionProvider -> poolConfig

io.lettuce.core.EpollProvider
io.lettuce.core.KqueueProvider 
epoll
可以理解为event poll，不同于忙轮询和无差别轮询，epoll之会把哪个流发生了怎样的I/O事件通知我们。此时我们对这些流的操作都是有意义的。epoll是Linux内核为处理大批量文件描述符而作了改进的poll，是Linux下多路复用IO接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率
Kqueue与epoll类似



## Redis连接工厂 - RedisConnectionFactory

RedisAutoConfiguration 负责：JedisConnectionFactory、RedisTemplate、StringRedisTemplate这3个功能类的创建


RedisConnectionFactory是一个接口，有如下4个具体的实现类，我们通常使用的是JedisConnectionFactory、LettuceConnectionFactory，也即是通过RedisConnection和RedisConnectionFactory接口，可以获取到Redis的活动连接




## Redis 缓存 (current)  -  >   存sessionId

缓存例子： https://howtodoinjava.com/spring-boot2/spring-boot-cache-example/


### 缓存数据库性能Redis、memcached、Ehcache
Redis存储数据类型丰富，对于存储数据量不是很大的情况下处理性能效果较好、支持持久化
memcached对于大量的数据存储和读取性能要优于Redis、没有持久化
Ehcache最大的特点是轻量级，而且存储的数据类型为对象


Spring Redis通过org.springframework.data.redis.cache包提供了Spring缓存抽象的实现


- 1.引入依赖 pom.xml

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

- 2.

Spring Cache集成redis的运行原理：

Spring缓存抽象模块通过CacheManager来创建、管理实际缓存组件，当SpringBoot应用程序引入spring-boot-starter-data-redi依赖后吗，容器中将注册的是CacheManager实例RedisCacheManager对象，RedisCacheManager来负责创建RedisCache作为缓存管理组件，由RedisCache操作redis服务器实现缓存数据操作。实际测试发现默认注入的RedisCacheManager操作缓存用的是RedisTemplate<Object, Object>，因此我们需要自定义cacheManager，替换掉默认的序列化器


- 自定义cacheManager，替换掉默认的序列化器



我们要把一个查询函数加入缓存功能，大致需要三步。

一、在函数执行前，我们需要先检查缓存中是否存在数据，如果存在则返回缓存数据。
二、如果不存在，就需要在数据库的数据查询出来。
三、最后把数据存放在缓存中，当下次调用此函数时，就可以直接使用缓存数据，减轻了数据库压力






![avatar](E:\markdown笔记\images\redis_cache.jpeg)



## spring cache 介绍
一套基于spring aop的方式 为函数添加缓存的 框架

支持的缓存类型
Generic
JCache (JSR-107)
EhCache 2.x
Hazelcast
Infinispan
Redis
Guava
Simple
如果不满足上述的缓存方案 可以自实现 cacheManager


Spring Cache 对 Cahce 进行了抽象，提供了 @Cacheable、@CachePut、@CacheEvict 等注解。Spring Boot 应用基于 Spring Cache，既提供了基于内存实现的缓存管理器，可以用于单体应用系统，也集成了 EhCache、Redis 等缓存服务器，可以用于大型系统或者分布式系统。

二、关于 Cache

应用系统需要通过 Cache 来缓存不经常改变的数据以提高系统性能和增加系统吞吐量，避免直接访问数据库等低速的存储系统。缓存的数据通常存放在访问速度更快的内存中或者是低延迟存取的存储器、服务器上。应用系统缓存通常有以下作用：

缓存 Web 系统的输出，如伪静态页面；

缓存系统中不经常改变的业务数据，如用户权限、字典数据、配置信息等。

三、使用 Spring Cache

Spring Boot 自带了基于 ConcurrentHashMap 的 Simple 缓存管理器，也集成了 EhCache、Redis 等缓存管理器。Spring Boot 应用通过注解的方式使用统一的缓存，只需要在方法上使用缓存注解即可，其缓存的具体实现依赖于选择的目标缓存管理器。本节先介绍 Spring Boot 自带的 Simple 缓存，然后再介绍 EhCahce 和 Redis 缓存。需要注意的是，Simple 只适合单体应用或者开发环境使用，再或者是一个小微系统，通常应用为分布式应用时，则需要集成 EhCache、Redis 等分布式缓存管理器。

3.1 添加 pom 依赖

集成 Spring Cache，只需要在 pom 中添加以下依赖：

<!-- Spring Cache -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
3.2 配置缓存管理器

在 application.properties 中配置目标缓存管理器：

spring.cache.type=redis
3.3 开启缓存功能

在启动类上添加 @EnableCaching 注解，开启缓存功能：

复制代码
package com.light.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

/**
 * 该注解指定项目为springboot，由此类当作程序入口，自动装配web依赖的环境
 * @author Administrator
 *
 */
@SpringBootApplication
@EnableCaching
public class SpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootApplication.class, args);
    }

}
复制代码
3.4 常用 Spring Cache 缓存注解

一旦配置好 Spring Boot 缓存，就可以在 Spring 管理的 Bean 中使用缓存注解，通常可以直接放在 Service 类上。

@CacheConfig，在类上设置当前缓存的一些公共设置，比如缓存名称；

@Cacheable，作用在方法上，触发缓存读取操作。表明该方法的结果是可以缓存的，如果缓存存在，则目标方法不会被调用，直接取出缓存。可以为方法声明多个缓存，如果至少有一个缓存有缓存项，则其缓存项将被返回；

@CacheEvice，作用在方法上，触发缓存失效操作，删除缓存项或者清空缓存；

@CachePut，作用在方法上，触发缓存更新操作，添加该注解后总是会执行方法体，并且使用返回的结果更新缓存，同 Cacheable 一样，支持 condition、unless、key 选项，也支持 KeyGenerator；

@Caching，作用在方法上，综合上面的各种操作，在有些场景上，调用业务会触发多种缓存操作。

通常清空下，直接使用 SpEL 表达式来指定缓存项的 Key 比自定义一个 KeyGenerator 更为简单。




What is the maximum number of keys a single Redis instance can hold? and what the max number of elements in a Hash, List, Set, Sorted Set?
Redis can handle up to 2^32 keys, and was tested in practice to handle at least 250 million keys per instance.
Every hash, list, set, and sorted set, can hold 2^32 elements.

In other words your limit is likely the available memory in your system.

一个key或是value大小最大是512M
单例能处理key：2.5亿个







## Redis 缓存

### 一是数据缓存

project: redis-cache

使用Redis做为数据缓存是最常用的场景。绝大多数的网站/系统，最先遇到的一个性能瓶颈就是数据库，使用Redis做数据库的前置缓存，可以有效的降低数据库的压力，从而提升整个系统的响应效率和并发量。Spring Boot也提供了非常简单的解决方案。

- 1.引入依赖 pom.xml

spring-boot-starter-cache会进行缓存的自动化配置和识别，Spring Boot为Redis自动配置了RedisCacheConfiguration等信息，具体redis配置参看上篇
https://zhuanlan.zhihu.com/p/65619273

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

- 2.RedisConfig配置

需要使用@EnableCaching启用缓存配置，否则出现注解不生效的情况；对于存储对象，使用Jackson进行序列化。

```
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport
{
    @Bean
    public RedisTemplate<String, Serializable> redisTemplate(LettuceConnectionFactory connectionFactory)
    {
        RedisTemplate<String, Serializable> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setConnectionFactory(connectionFactory);
        return redisTemplate;
    }
}
```

#### 缓存注解
@Cacheable、@CachePut、@CacheEvict

- A.@Cacheable 
用来声明方法是可缓存的，将结果存储到缓存中以便后续使用相同参数调用时不需执行实际的方法，直接从缓存中取值。注解可以标记在一个方法上，也可以标记在一个类上，当标记在一个方法上时表示该方法是支持缓存的，当标记在一个类上时则表示该类所有的方法都是支持缓存的


- B、注解参数

@Cacheable 支持以下参数：

value：缓存的名称。
key：缓存的key，可以为空，如果指定要按照SpEL表达式编写，如果不指定，则缺省按照方法的所有参数进行组合。
condition：触发条件，只有满足条件的情况才会加入缓存，默认为空，既表示全部都加入缓存，支持SpEL

@Cacheable(value ="userCache") 当调用这个方法的时候，会从一个key为userCache:id的缓存中查询，如果没有，则执行实际的方法，并将返回的结果存入缓存中；否则返回缓存中的对象


- C、@CachePut

项目运行中会对数据库的信息进行更新，如果仍然使用@Cacheable就会导致数据库的信息和缓存的信息不一致。项目中，一般更新完数据库后，再手动删除掉Redis中对应的缓存，以保证数据的一致性。Spring Boot提供了另外的一种解决方案，可以优雅的去更新缓存

与@Cacheable不同的是使用@CachePut注解的方法在执行前，不会去检查缓存中是否存在之前执行过的结果，而是每次都会执行该方法，并将执行结果以键值对的形式存入指定的缓存中

- D、@CacheEvict

@CacheEvict是用来标注在需要清除缓存元素的方法或类上的，当标记在一个类上时表示其中所有的方法的执行都会触发缓存的清除操作。

@CacheEvict可以指定的参数有value、key、condition、allEntries 和 beforeInvocation。其中value、key 和 condition的语义与@Cacheable对应的属性类似。

1.allEntries属性

allEntries是boolean类型，表示是否需要清除缓存中的所有元素。默认为false，表示不需要，当指定了allEntries为true时，Spring Cache将忽略指定的key。有的时候需要一次清除Cache中所有的元素，比一个一个清除元素更有效率。

@RequestMapping("/delete")
@CacheEvict(value = "userCache", beforeInvocation = true, allEntries = true)
public Integer delete(Integer id)
{
    int rs = userMapper.delete(id);
    if (rs != 1)
    {
        throw new RuntimeException("delete user exception");
    }
    return rs;
}

2、beforeInvocation属性
清除操作默认是在对应方法成功执行之后触发的，即方法如果因为抛出异常而未能成功返回时也不会触发清除操作。使用beforeInvocation可以改变触发清除操作的时间，当指定该属性值为true时，Spring会在调用该方法之前清除缓存中的指定元素。

@Cacheable、@CacheEvict、@CachePut三个注解非常灵活，满足了我们对数据缓存的绝大多数使用场景，并且使用起来非常的简单而又强大,在实际工作中我们可以灵活搭配使用。


### 使用RedisTemplate模板自定义缓存

#### 响应式redis reactive

project: redis-cache-webflux (有问题)

- 1.引入依赖

使用响应式的redis依赖spring-boot-starter-data-redis-reactive

```

```

目前，@Cacheable 等注解形式实现缓存没有很好的集成，二者 Mono / Flux 对象没有实现 Serializable，无法通过默认序列化器，解决方式是需要自定义序列化，这里通过手动方式与 Redis 手动集成，并实现缓存策略。






### Session共享

把servlet容器实现的httpSession替换为spring-session，Spring Session提供了一套创建和管理Servlet HttpSession的方案。Spring Session提供了集群Session（Clustered Sessions）功能，默认采用外置的Redis来存储Session数据，以此来解决Session共享的问题

https://www.cnblogs.com/lxyit/p/9672097.html 图可用

https://zhuanlan.zhihu.com/p/65871584

- A、Session简介
由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，需要用某种机制来识具体的用户。Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上，就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

在互联网行业中用户量访问巨大，往往需要多个节点共同对外提供某一种服务。


用户的请求首先会到达前置网关，前置网关根据负载均衡策略将请求分发到后端的服务器，就会出现第一次的请求会交给服务器A处理，下次的请求可能会是服务B处理，如果不做Session共享的话，就有可能出现用户在服务器A登录了，下次请求的时候到达服务器B又要求用户重新登录。

前置网关一般使用LVS、nginx或者F5等软硬件，有些软件可以指定策略让用户每次请求都分发到同一台服务器中，但是当其中一台服务Down掉之后，就会出现一批用户交易失效。在实际工作中建议使用外部的缓存设备来共享Session，避免单个节点挂掉而影响服务，使用外部缓存Session后，我们的共享数据都会放到外部缓存容器中，服务本身就会变成无状态的服务，可以随意的根据流量的大小增加或者减少负载的设备。

一般Tomcat有自带的Session共享功能，但是使用起来并不是很便利，官方也不推荐大规模的使用，最常见的方式就是使用Redis来实现后端服务的Session共享。

- B、Spring Session
Spring Session提供了一套创建和管理Servlet HttpSession的方案。Spring Session提供了集群Session（Clustered Sessions）功能，默认采用外置的Redis来存储Session数据，以此来解决Session共享的问题。

Spring Session为企业级Java应用的session管理带来了革新，使得以下的功能更加容易实现：

API和用于管理用户会话的实现。
HttpSession - 允许以应用程序容器（Tomcat）中性的方式替换HttpSession。
将session所保存的状态卸载到特定的外部session存储中，它们能够以独立于应用服务器的方式提供高质量的集群。
支持每个浏览器上使用多个session，从而能够很容易地构建更加丰富的终端用户体验。
控制session id如何在客户端和服务器之间进行交换，这样的话就能很容易地编写Restful API，因为它可以从HTTP头信息中获取session id，而不必再依赖于cookie。
当用户使用WebSocket发送请求的时候，能够保持HttpSession处于活跃状态。
PS：Spring Session核心项目并不依赖于Spring框架，所以能够将其应用于不使用Spring框架的项目中。


- 1.引入依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.session</groupId>
    <artifactId>spring-session-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```

使用的是Redis来存储应用的session，当然spring session还支持以下几种方式：
EnableGemFireHttpSession
EnableMongoHttpSession
EnableJdbcHttpSession

- 2.Session配置

```
@Configuration
@EnableRedisHttpSession(maxInactiveIntervalInSeconds = 86400 * 30)
public class SessionConfig
{

}
```

开启spring session的配置，该配置会负责创建一个Servlet的过滤器:SessionRepositoryFilter，该过滤器负责包装HttpServletRequest，并将替换HttpSession的实现。

HttpSession策略
默认的情况下，使用的是CookieHttpSessionStrategy，这种策略将允许使用setCookieName来指定Cookie的名称，如果不指定，默认是SESSION。当一个会话被创建时，HTTP响应将会有一个包含指定cookie名称和session id的cookie。spring session也支持其他的策略，如下：

HeaderHttpSessionStrategy
MultiHttpSessionStrategyAdapter

HeaderHttpSessionStrategy：
所幸的是Spring Session还支持另一种方式:将sessionid存储在Httpheader里,这样可以不借助cookie而获取sessionid从而回去session 

如何配置HttpSession策略，方式如下：
默认使用的是CookieHttpSessionStrategy
```
@Configuration
public class HttpSessionConfig {  
  
    @Bean  
    public HttpSessionStrategy httpSessionStrategy() {  
        return new CookieHttpSessionStrategy();
    }  
}
```  

session信息被过滤器拦截之后，会存放在redis中
Spring-Session是通过过滤器实现的session共享


在实际的使用中，我们发现每次请求经过zuul的时候，session就会发生改变，导致后面的请求获取不到session中的内容，该问题疑似是cookie丢失导致的，目前的解决方案是每次请求的时候，前端都将cookie信息带过来，从而获取session里面的信息，如有更好的解决方案，还请不吝赐教


### spring-session管理session分析

1、DelegatingFilterProxy代理类
DelegatingFilterProxy -> initFilterBean -> getFilterName

没有配置targetBeanName，则直接使用filter-name，这里指定的是springSessionRepositoryFilter，这个名称是一个固定值此filter在RedisHttpSessionConfiguration中被定义

2、RedisHttpSessionConfiguration配置类
SpringHttpSessionConfiguration -> springSessionRepositoryFilter
此方法返回值是SessionRepositoryFilter，这个其实就是真实的过滤器；方法参数sessionRepository同样使用@Bean注解的方式定义

此方法的返回值是RedisOperationsSessionRepository，有关于session持久化到redis的相关操作都在此类中；
注：持久化到redis只是spring-session的一种方式，也支持持久化到其他数据库中（jdbc，Mongo，Hazelcast等）

在RedisHttpSessionConfiguration的父类SpringHttpSessionConfiguration中定义了springSessionRepositoryFilter

3、SessionRepositoryFilter过滤器
SessionRepositoryFilter

所有的请求都会先经过SessionRepositoryFilter过滤器，doFilter方法如下

request被包装成了SessionRepositoryRequestWrapper对象，response被包装成了SessionRepositoryResponseWrapper对象，SessionRepositoryRequestWrapper中重写了getSession等方法；finally中执行了commitSession方法，将session进行持久化操作

4、SessionRepositoryRequestWrapper包装类
重写的getSession方法
大致分为三步，首先去本地内存中获取session，如果获取不到去指定的数据库中获取，这里其实就是去redis里面获取，sessionRepository就是上面定义的RedisOperationsSessionRepository对象；如果redis里面也没有则创建一个新的session

5、RedisOperationsSessionRepository类

关于session的保存，更新，删除，获取操作都在此类中；



流程图及解析：
来自于：
https://www.cnblogs.com/nick-huang/p/6986824.html#my_inner_label10

2. 委托过滤器代理类，DelegatingFilterProxy
3. Spring Session主要配置类，RedisHttpSessionConfiguration
4. Session存储过滤器，SessionRepositoryFilter
5. HTTP请求包装类，SessionRepositoryRequestWrapper
6. Http请求与Session的关系策略，HttpSessionStrategy
   6.1. Cookie方式，CookieHttpSessionStrategy
   6.2. HTTP请求头方式，HeaderHttpSessionStrategy
7. Session持久化，SessionRepository



还未写完：
来自于：
http://www.sohu.com/a/260010804_464962



springsession 使用cookie传递session id，每个cookie的path不一致。 springsession默认使用DefaultCookieSerializer 来序列化cookie信息




## Redis连接工厂JedisConnectionFactory 与 RedisTemplate


Jedis是Redis官方推荐的面向Java的操作Redis的客户端，而RedisTemplate是SpringDataRedis中对JedisApi的高度封装。
SpringDataRedis相对于Jedis来说可以方便地更换Redis的Java客户端，比Jedis多了自动管理连接池的特性，方便与其他Spring框架进行搭配使用如：SpringCache

RedisTemplate是 Spring对jedis的封装，所以不需要使用Redis的连接工厂JedisConnectionFactory，Spring代为管理






禁用cookie后如何使用session还有session_id的使用
https://www.cnblogs.com/zekeny/p/6708152.html


## session 持久化
我们知道session其实是在cookie中保存了一个sessionid，用户每次访问都将sessionid发给服务器，服务器通过ID查找用户对应的状态数据


## session 共享
使用 Redis 实现 Session 共享
https://segmentfault.com/a/1190000019625173
将 Session 所保存的状态卸载到特定的外部 Session 存储中，如 Redis 或 Apache Geode
中，它们能够以独立于应用服务器的方式提供高质量的集群； 

支持每个浏览器上使用多个Session，从而能够很容易地构建更加丰富的终端用户体验；

控制 Session ID如何在客户端和服务器之间进行交换，这样的话就能很容易地编写 Restful API，因为它可以从 HTTP 头信息中获取 Session
ID，而不必再依赖于 cookie； 

当用户使用 WebSocket 发送请求的时候，能够保持 HttpSession 处于活跃状态。

需要说明的很重要的一点就是，Spring Session 的核心项目并不依赖于 Spring 框架，因此，我们甚至能够将其应用于不使用
Spring 框架的项目中。

https://blog.csdn.net/lanshen110119/article/details/83104926



Spring Session提供了用于管理用户会话信息的API和实现。

特征
Spring Session使得支持集群会话变得微不足道，而不依赖于特定于应用程序容器的解决方案。它还提供透明集成：
HttpSession - 允许以应用程序容器（即Tomcat）中立的方式替换HttpSession，支持在头文件中提供会话ID以使用RESTful API
WebSocket - 提供在接收WebSocket消息时保持HttpSession活动的能力
WebSession - 允许以应用程序容器中立方式替换Spring WebFlux的WebSession

模块
Spring Session包含以下模块：
Spring Session Core - 提供核心Spring Session功能和API
Spring会话数据Redis - 提供由Redis支持的SessionRepository和ReactiveSessionRepository实现以及配置支持
Spring Session JDBC - 提供由关系数据库和配置支持支持的SessionRepository实现
Spring Session Hazelcast - 提供由Hazelcast和配置支持支持的SessionRepository实现



2.0版本的浏览器cookie保存的值：

Cookie:

ffd2dfec-425e-4516-89b6-c1b2e27c85af
Cookie: SESSION=ZmZkMmRmZWMtNDI1ZS00NTE2LTg5YjYtYzFiMmUyN2M4NWFm


浏览器cookie里存的sessionId是经过base64编码过的，而服务器上获取的sessionId是没有编码的。cookie里的解码后与服务器上其实是一致的。这里的关键是spring session 在设置cookie的时候设置的，可以看源码 DefaultCookieSerializer 类里面的writeCookieValue 方法，里面有关键的一段代码

Cookie sessionCookie = new Cookie(this.cookieName, this.useBase64Encoding
      ? base64Encode(actualCookieValue) : actualCookieValue);
而在类的成员变量里面是直接指定了

private boolean useBase64Encoding = true;



https://blog.csdn.net/lanshen110119/article/details/83104926
springCloud+Redis实现session共享技术

springboot系列文章之 集成redis 服务 (Lettuce & Jedis)
https://juejin.im/post/5ba0a098f265da0adb30c684


## lettuce 与 jedis

https://www.cnblogs.com/hujunzheng/p/9660681.html






基于 spring-session 解决分布式 session 共享问题：
https://segmentfault.com/a/1190000011091273

https://juejin.im/post/5c1e71176fb9a04a0b222929

### 方案一由 cookie 管理 sessionid
### 方案二由 httpheader 管理 sessionid





因为需要将对象序列化后存储到 Redis。如果没实现 Serializable，
 * 会引出异常：java.lang.IllegalArgumentException: DefaultSerializer requires a Serializable
 * payload but received an object of type。
 如果不是用默认的序列化，需要自定义序列化实现，只要实现 RedisSerializer 接口去实现即可，
 然后在使用 RedisTemplate.setValueSerializer 方法去设置你实现的序列化实现，支持 JSON、XML 等



应用场景：
缓存：相对静态、或变化缓慢的数据，可以利用缓存降低数据库IO压力，提升性能
分布式锁：集群环境下对同一个资源的竞争，可借助redis来实现对资源加锁。可查阅官方redlock相关介绍
秒杀：在redis中进行预处理，充当缓冲，将处理结果延迟持久化到数据库。规避高并发对DB的压力
计算器：每次操作加1，redis天然支持计数的api操作
消息队列：功能太简单，一般不用
分布式session

限数：每60秒获取一次验证码，设置key、value、失效时间1分钟到redis，每次获取校验码进行先验证redis是否有值即可



任务：
多个master redis集群部署

redis集群搭建：
https://www.jianshu.com/p/8045b92fafb2

redis分布式锁：
https://www.cnblogs.com/rgcLOVEyaya/p/RGC_LOVE_YAYA_1003days.html



redis的工作模式：
单进程、 阻塞式 
同一时刻只能处理一个请求，后面的请求需要排队等待

redis server有一个进程，包含了多个线程，其中只有一个线程用于处理客户端请求。

优点：无需处理并发问题，降低系统复杂度
缺点：不适合大对象 100kb


### redis 计数器

频率控制：接口防刷，密码尝试次数限制

数量统计：请求量统计

数量控制：商品抢购，奖励额度控制



数量控制器

商品抢购。没控制住，库存不够了，成本失控
抽奖限量，
抢红包


redis cluster
replication + sentinal
集群：解析与原理分析
https://www.cnblogs.com/mengchunchen/p/10059436.html
https://cloud.tencent.com/developer/article/1489653

## 分布式锁 Distributed Locks

### 锁的概念：
悲观锁：

　　 
乐观锁： 
　　 
　　 





分布式锁解决并发的三种实现方式
在很多场景中，我们为了保证数据的最终一致性，需要很多的技术方案来支持，比如分布式事务、分布式锁等。有的时候，我们需要保证一个方法在同 一时间内只能被同一个线程执行。在单机环境中，Java中其实提供了很多并发处理相关的API，但是这些API在分布式场景中就无能为力了。也就是说单纯的Java Api并不能提供分布式锁的能力。所以针对分布式锁的实现目前有多种方案：

分布式锁一般有三种实现方式：

1、 数据库锁
2、基于Redis的分布式锁
3、基于ZooKeeper的分布式锁



基于Redis的分布式锁的实现
https://juejin.im/post/5cc165816fb9a03202221dd5
https://mp.weixin.qq.com/s?__biz=MzU5ODUwNzY1Nw==&mid=2247484155&idx=1&sn=0c73f45f2f641ba0bf4399f57170ac9b&scene=21#wechat_redirect

https://blog.csdn.net/kongmin_123/article/details/82080962



## 分布式锁
Spring Cloud分布式微服务系统中利用redssion实现分布式锁
https://www.cnblogs.com/shamo89/p/8036451.html
https://github.com/redisson/redisson/tree/master/redisson-spring-boot-starter#spring-boot-starter
https://www.cnblogs.com/yangzhilong/p/7605807.html

https://github.com/redisson/redisson/wiki/8.-%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81%E5%92%8C%E5%90%8C%E6%AD%A5%E5%99%A8

https://blog.csdn.net/Pa_Java/article/details/85630526


https://blog.csdn.net/zhuyu19911016520/article/details/84947566
https://gitee.com/zhuyu1991/spring-cloud/tree/master/distributed-lock



https://gitee.com/ztp/redisson-spring-boot-starter


redisson-spring-boot-starter
lettue实现分布式锁




spring 分布式锁-RedisLockRegistry和ZookeeperLockRegistry
https://blog.csdn.net/l18767118724/article/details/85261092

https://www.cnblogs.com/xuwenjin/p/10681187.html
https://www.cnblogs.com/fengyuduke/p/10678122.html

https://juejin.im/post/5b16148a518825136137c8db

http://www.manongjc.com/article/34632.html


spring-integration：
https://spring.io/projects/spring-integration


RedisLockRegistry:
https://wangkang007.gitbooks.io/java/content/redislockregistry.html


基于redis理论的cas（check and set）乐观锁
























