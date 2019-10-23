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

## Redis的String数据结构 （推荐直接使用StringRedisTemplate）
注意：如果使用RedisTemplate需要更改序列化方式
```
	RedisSerializer<String> stringSerializer = new StringRedisSerializer();
	template.setKeySerializer(stringSerializer );
	template.setValueSerializer(stringSerializer );
	template.setHashKeySerializer(stringSerializer );
	template.setHashValueSerializer(stringSerializer );
```
public interface ValueOperations<K,V>
Redis operations for simple (or in Redis terminology 'string') values.
ValueOperations可以对String数据结构进行操作：

- set void set(K key, V value);

```
redisTemplate.opsForValue().set("name","StringRedisTemplate");
```

- 
- 
- 
-  V get(Object key);


```
redisTemplate.opsForValue().get("name");  // StringRedisTemplate
```


- get String get(K key, long start, long end);
截取key所对应的value字符串

```
System.out.println(redisTemplate.opsForValue().get("name")); // StringRedisTemplate
System.out.println(redisTemplate.opsForValue().get("name",0,5));// "Strin
System.out.println(redisTemplate.opsForValue().get("name",0,-1));// "StringRedisTemplate"
System.out.println(redisTemplate.opsForValue().get("name",-3,-1));// te"
```


- set void set(K key, V value, long timeout, TimeUnit unit);

```
redisTemplate.opsForValue().set("name","tom",10, TimeUnit.SECONDS);
```

- set void set(K key, V value, long offset);
该方法是用 value 参数覆写(overwrite)给定 key 所储存的字符串值，从偏移量 offset 开始


- Boolean setIfAbsent(K key, V value);

```
redisTemplate.opsForValue().setIfAbsent("name","nick");
System.out.println(redisTemplate.opsForValue().get("name")); // StringRedisTemplate
```


- multiSet void multiSet(Map<? extends K, ? extends V> m);
为多个键分别设置它们的值

- multiGet List<V> multiGet(Collection<K> keys);
为多个键分别取出它们的值

```
Map<String,String> maps = new HashMap<String, String>();
maps.put("multi1","multi1");
maps.put("multi2","multi2");
maps.put("multi3","multi3");
template.opsForValue().multiSet(maps);

List<String> keys = new ArrayList<String>();
keys.add("multi1");
keys.add("multi2");
keys.add("multi3");
System.out.println(template.opsForValue().multiGet(keys)); // [multi1, multi2, multi3]
```

- Boolean multiSetIfAbsent(Map<? extends K, ? extends V> m);
为多个键分别设置它们的值，如果存在则返回false,不更新;不存在返回true,同时set进去

```
Map<String,String> maps1 = new HashMap<String, String>();
maps.put("multi11","multi111");
maps.put("multi22","multi22");
maps.put("multi33","multi33");
Map<String,String> maps2 = new HashMap<String, String>();
maps2.put("multi1","multi1");
maps2.put("multi2","multi2");
maps2.put("multi3","multi3");
System.out.println(redisTemplate.opsForValue().multiSetIfAbsent(maps)); // true，同时set进去
System.out.println(redisTemplate.opsForValue().multiSetIfAbsent(maps2));// false，不更新

```


- getAndSet V getAndSet(K key, V value);
设置键的字符串值并返回其旧值

```
redisTemplate.opsForValue().set("getSetTest","test");
System.out.println(redisTemplate.opsForValue().getAndSet("getSetTest","test2")); // test
System.out.println(redisTemplate.opsForValue().get("getSetTest")); // test2
```

- increment Long increment(K key, long delta);
支持整数

```
Long increlong = redisTemplate.opsForValue().increment("increlong", 1);
System.out.println(increlong); // 1
```

- increment Double increment(K key, double delta);
也支持浮点数

```
Double increlong1 = redisTemplate.opsForValue().increment("increlong", 1.2);
System.out.println(increlong1); // 2.2
```

- Integer append(K key, String value);
如果key已经存在并且是一个字符串，则该命令将该值追加到字符串的末尾。如果键不存在，则它被创建并设置为空字符串，因此APPEND在这种特殊情况下将类似于SET。

```
System.out.println(redisTemplate.opsForValue().get("appendTest")); // null
redisTemplate.opsForValue().append("appendTest","hello");
redisTemplate.opsForValue().append("appendTest","hello"); // hellohello
      
```


- size Long size(K key);
返回key所对应的value值得长度

```
System.out.println(redisTemplate.opsForValue().size("name")); // 21  "StringRedisTemplate"
```


## Redis的List数据结构
public interface ListOperations<K,V>
Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）
ListOperations专门操作list列表：

- Long leftPush(K key, V value);
将所有指定的值插入存储在键的列表的头部。如果键不存在，则在执行推送操作之前将其创建为空列表。（从左边插入）

```
redisTemplate.opsForList().leftPush("list","java");
redisTemplate.opsForList().leftPush("list","python");
Long list = redisTemplate.opsForList().leftPush("list", "c++"); // [c++, python, java]
```
- Long leftPushAll(K key, V... values);
批量把一个数组插入到列表中

```
String[] a_str = new String[]{"1","2","3"};
Long listarray = redisTemplate.opsForList().leftPushAll("listarray", a_str); // [3, 2, 1]
```

- Long leftPushAll(K key, Collection<V> values);
批量把一个集合插入到列表中

```
List<Object> strings = new ArrayList<Object>();
strings.add("1");
strings.add("2");
strings.add("3");
redisTemplate.opsForList().leftPushAll("listcollection", strings); // [3, 2, 1]
```

- Long leftPushIfPresent(K key, V value);
只有存在key对应的列表才能将这个value值插入到key所对应的列表中

```
System.out.println(redisTemplate.opsForList().leftPushIfPresent("leftPushIfPresent","aa")); // 0  此时无leftPushIfPresent
System.out.println(redisTemplate.opsForList().leftPushIfPresent("leftPushIfPresent","bb")); // 0  此时无leftPushIfPresent
System.out.println("===========================================================");
System.out.println(redisTemplate.opsForList().leftPush("leftPushIfPresent","aa")); // 1，因为list的长度为1; 此时leftPushIfPresent:[aa]
System.out.println(redisTemplate.opsForList().leftPushIfPresent("leftPushIfPresent","bb")); // 1，因为list的长度为1; 此时leftPushIfPresent:[bb,aa]
```

- Long leftPush(K key, V pivot, V value);
把value值放到key对应列表中pivot值的左边，如果pivot值存在的话

```
System.out.print(redisTemplate.opsForList().range("list",0,-1)); // [python, java]
redisTemplate.opsForList().leftPush("list","java","oc");
System.out.print(redisTemplate.opsForList().range("list",0,-1)); // [python, oc, java]
```

- List<V> range(K key, long start, long end);
返回存储在键中的列表的指定元素。偏移开始和停止是基于零的索引，其中0是列表的第一个元素（列表的头部），1是下一个元素

-void trim(K key, long start, long end);
修剪现有列表，使其只包含指定的指定范围的元素，起始和停止都是基于0的索引

```
redisTemplate.opsForList().trim("list",1,-1);//裁剪第一个元素
System.out.println(redisTemplate.opsForList().range("list",0,-1)); // [python, java]
```

- Long size(K key);
返回存储在键中的列表的长度。如果键不存在，则将其解释为空列表，并返回0。当key存储的值不是列表时返回错误。

```
System.out.println(redisTemplate.opsForList().size("list")); // 2
```

- Long rightPush(K key, V value);
将所有指定的值插入存储在键的列表的头部。如果键不存在，则在执行推送操作之前将其创建为空列表。（从右边插入）

- Long rightPushAll(K key, V... values);

- Long rightPushAll(K key, Collection<V> values);

- Long rightPushIfPresent(K key, V value);
只有存在key对应的列表才能将这个value值插入到key所对应的列表中

- Long rightPush(K key, V pivot, V value);
把value值放到key对应列表中pivot值的右边，如果pivot值存在的话

- void set(K key, long index, V value);
在列表中index的位置设置value值

```
System.out.println(redisTemplate.opsForList().range("list",0,-1)); // [python, oc, java]
redisTemplate.opsForList().set("list",1,"setValue");
System.out.println(redisTemplate.opsForList().range("list",0,-1)); // [python, setValue, java]
```
- Long remove(K key, long count, Object value);
从存储在键中的列表中删除等于值的元素的第一个计数事件。
计数参数以下列方式影响操作：
count > 0：删掉从左边找 |count| 个等于value的值
count <0：删掉从右边找 |count| 个等于value的值 
count = 0：删除所有等于value的元素

```
 List<Object> string = new ArrayList<Object>();
        string.add("a");
        string.add("b");
        string.add("c");
        string.add("a");
        string.add("b");
        string.add("c");
        redisTemplate.opsForList().leftPushAll("removelist", string);
        System.out.println(redisTemplate.opsForList().range("removelist",0,-1)); // [c, b, a, c, b, a]
        redisTemplate.opsForList().remove("removelist",1,"a");//count > 0  删掉从左边找 |count| 个等于value 的值                  [c, b, c, b, a]
//        redisTemplate.opsForList().remove("removelist",-1,"a");//count < 0  删掉从右边找 |count| 个等于value 的值                            [c, b, c, b]
//        redisTemplate.opsForList().remove("removelist",0,"c");//count = 0  删掉所有等于value 的值                                            [b,b]
        System.out.println(redisTemplate.opsForList().range("removelist",0,-1));
```

- V index(K key, long index);
根据下表获取列表中的值，下标是从0开始的

```
System.out.println(redisTemplate.opsForList().range("removelist",0,-1)); // [b,b]
System.out.println(redisTemplate.opsForList().index("removelist",1));        // [b]
```

- V leftPop(K key);
弹出最左边的元素，弹出之后该值在列表中将不复存在

```
System.out.println(redisTemplate.opsForList().range("list",0,-1));  // [python, setValue, java]
System.out.println(redisTemplate.opsForList().leftPop("list"));           //  python
System.out.println(redisTemplate.opsForList().range("list",0,-1)); //  [setValue, java]
```

- V leftPop(K key, long timeout, TimeUnit unit);
移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止


- V rightPop(K key);
弹出最右边的元素，弹出之后该值在列表中将不复存在

- V rightPop(K key, long timeout, TimeUnit unit);
移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。


- V rightPopAndLeftPush(K sourceKey, K destinationKey);
用于移除列表的最后一个元素，并将该元素添加到另一个列表并返回。

```
System.out.println(redisTemplate.opsForList().range("list",0,-1));  // [setValue, java]
System.out.println(redisTemplate.opsForList().range("removelist",0,-1)); // [b, b]
redisTemplate.opsForList().rightPopAndLeftPush("list","removelist");
System.out.println(redisTemplate.opsForList().range("list",0,-1));     // [setValue]
System.out.println(redisTemplate.opsForList().range("removelist",0,-1)); // [java, b, b]
```
- V rightPopAndLeftPush(K sourceKey, K destinationKey, long timeout, TimeUnit unit);
用于移除列表的最后一个元素，并将该元素添加到另一个列表并返回，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。



## Redis的Hash数据机构
Redis的散列可以让用户将多个键值对存储到一个Redis键里面
public interface HashOperations<H,HK,HV>
HashOperations提供一系列方法操作hash

- void put(H key, HK hashKey, HV value);
设置散列hashKey的值

```
redisTemplate.opsForHash().put("redisHash","name","nick");
redisTemplate.opsForHash().put("redisHash","name","tom");
redisTemplate.opsForHash().put("redisHash","age","26");
redisTemplate.opsForHash().put("redisHash","class","6");
```

- void putAll(H key, Map<? extends HK, ? extends HV> m);
使用m中提供的多个散列字段设置到key对应的散列表中

```
Map<String,Object> testMap = new HashMap();
testMap.put("name","jack");
testMap.put("age","27");
testMap.put("class","1");
redisTemplate.opsForHash().putAll("redisHash1",testMap);
```
- Boolean putIfAbsent(H key, HK hashKey, HV value);
仅当hashKey不存在时才设置散列hashKey的值

```
System.out.println(redisTemplate.opsForHash().putIfAbsent("redisHash","age","30"));
System.out.println(redisTemplate.opsForHash().putIfAbsent("redisHash","kkk","kkk"));
```

- HV get(H key, Object hashKey);
从键中的哈希获取给定hashKey的值

```
System.out.println(redisTemplate.opsForHash().get("redisHash","age"));  // 26
```

- List<HV> multiGet(H key, Collection<HK> hashKeys);
从哈希中获取给定hashKey的值

```
List<Object> kes = new ArrayList<Object>();
kes.add("name");
kes.add("age");
System.out.println(redisTemplate.opsForHash().multiGet("redisHash",kes));  // [tom, 26]
```

- Set<HK> keys(H key);
获取key所对应的散列表的key

```
 System.out.println(redisTemplate.opsForHash().keys("redisHash")); // [name, age, class, kkk]
```

- List<HV> values(H key);
获取整个哈希存储的值根据密钥

```
System.out.println(redisTemplate.opsForHash().values("redisHash")); // [tom, 26, 6, kkk]
```

- Map<HK, HV> entries(H key);
获取整个哈希存储根据密钥

```
System.out.println(redisTemplate.opsForHash().entries("redisHash")); // {name=tom, age=26, class=6, kkk=kkk}
```

- Cursor<Map.Entry<HK, HV>> scan(H key, ScanOptions options);
使用Cursor在key的hash中迭代，相当于迭代器。

```
Cursor<Map.Entry<Object, Object>> curosr = redisTemplate.opsForHash().scan("redisHash", ScanOptions.NONE);
while(curosr.hasNext()){
    Map.Entry<Object, Object> entry = curosr.next();
    System.out.println(entry.getKey()+":"+entry.getValue());
    // name:tom
    //age:26
    //class:6
    //kkk:kkk
}
```
- Long size(H key);
获取key所对应的散列表的大小个数

```
System.out.println(redisTemplate.opsForHash().size("redisHash")); // 4
```

- Long delete(H key, Object... hashKeys);
删除给定的哈希hashKeys

```
System.out.println(redisTemplate.opsForHash().delete("redisHash","kkk")); // 1
System.out.println(redisTemplate.opsForHash().entries("redisHash"));    // {name=tom, age=26, class=6}
```

- Boolean hasKey(H key, Object hashKey);
确定哈希hashKey是否存在

```
System.out.println(redisTemplate.opsForHash().hasKey("redisHash","age")); // true
System.out.println(redisTemplate.opsForHash().hasKey("redisHash","kkk")); // false
```

- Long increment(H key, HK hashKey, long delta);
通过给定的delta增加散列hashKey的值（整型）

```
System.out.println(redisTemplate.opsForHash().get("redisHash","age")); // 26
System.out.println(redisTemplate.opsForHash().increment("redisHash","age",1)); // 27
```

- Double increment(H key, HK hashKey, double delta);
通过给定的delta增加散列hashKey的值（浮点数）

```
System.out.println(redisTemplate.opsForHash().get("redisHash","age")); // 27
 System.out.println(redisTemplate.opsForHash().increment("redisHash","age",1.1)); // 28.1
```

## Redis的Set数据结构
Redis的Set是string类型的**无序集合**。集合成员是唯一的，这就意味着集合中**不能出现重复**的数据。
Redis 中 集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。
public interface SetOperations<K,V>
SetOperations提供了对无序集合的一系列操作

- Long add(K key, V... values);
无序集合中添加元素，返回添加个数
也可以直接在add里面添加多个值 

```
redisTemplate.opsForSet().add("setTest","aaa","bbb");
String[] strarrays = new String[]{"strarr1","starr2"};
System.out.println(redisTemplate.opsForSet().add("setTest", strarrays)); // 2
```

- Set<V> members(K key);
- 返回集合中的所有成员

```
System.out.println(redisTemplate.opsForSet().members("setTest")); // [sgtarr2, aaa, strarr1, starr2, bbb]
```

- Boolean isMember(K key, Object o);
判断 member 元素是否是集合 key 的成员

```
System.out.println(redisTemplate.opsForSet().isMember("setTest","ccc")); // false
System.out.println(redisTemplate.opsForSet().isMember("setTest","aaa")); // true
```

- Long remove(K key, Object... values);
移除集合中一个或多个成员

```
String[] strarray = new String[]{"strarr1","starr2"};
System.out.println(redisTemplate.opsForSet().remove("setTest",strarray)); // 2
```

- V pop(K key);
移除并返回集合中的一个随机元素

```
System.out.println(redisTemplate.opsForSet().pop("setTest")); // aaa
System.out.println(redisTemplate.opsForSet().members("setTest")); // [bbb]
```

- Long size(K key);
无序集合的大小长度

```
System.out.println(redisTemplate.opsForSet().size("setTest")); // 1
```

- Boolean move(K key, V value, K destKey);
将 member 元素从 source 集合移动到 destination 集合 注：key:destination必须要先存在

```
redisTemplate.opsForSet().add("setTest2","aaa","bbb");
redisTemplate.opsForSet().move("setTest","bbb","setTest2");
System.out.println(redisTemplate.opsForSet().members("setTest")); // []
System.out.println(redisTemplate.opsForSet().members("setTest2"));// [aaa, bbb]
```


- Set<V> intersect(K key, K otherKey);
key对应的无序集合与otherKey对应的无序集合求交集

```
System.out.println(redisTemplate.opsForSet().intersect("setTest","setTest2")); // []
```

- Set<V> intersect(K key, Collection<K> otherKeys);
key对应的无序集合与多个otherKey对应的无序集合求交集

```
redisTemplate.opsForSet().add("setTest","aaa");
redisTemplate.opsForSet().add("setTest3","aaa","bbb");
List<String> strlist = new ArrayList<String>();
strlist.add("setTest2");
strlist.add("setTest3");
System.out.println(redisTemplate.opsForSet().intersect("setTest",strlist)); // [aaa]
```
- Long intersectAndStore(K key, K otherKey, K destKey);
key无序集合与otherkey无序集合的交集存储到destKey无序集合中 注：destKey 可以先不存在

```
System.out.println(redisTemplate.opsForSet().members("setTest"));// [aaa]
System.out.println(redisTemplate.opsForSet().members("setTest2")); // [aaa, bbb]
System.out.println(redisTemplate.opsForSet().intersectAndStore("setTest","setTest2","destKey1"));
System.out.println(redisTemplate.opsForSet().members("destKey1")); // [aaa]
```

- Long intersectAndStore(K key, Collection<K> otherKeys, K destKey);
key对应的无序集合与多个otherKey对应的无序集合求交集存储到destKey无序集合中


- Set<V> union(K key, K otherKey);
key无序集合与otherKey无序集合的并集

```
System.out.println(redisTemplate.opsForSet().union("setTest","setTest2")); // [aaa, bbb]
```

- Set<V> union(K key, Collection<K> otherKeys);
key无序集合与多个otherKey无序集合的并集


- Long unionAndStore(K key, K otherKey, K destKey);
key无序集合与otherkey无序集合的并集存储到destKey无序集合中

```
System.out.println(redisTemplate.opsForSet().members("setTest")); //  [aaa]
System.out.println(redisTemplate.opsForSet().members("setTest2")); // [aaa, bbb]
System.out.println(redisTemplate.opsForSet().unionAndStore("setTest","setTest2","unionAndStoreTest")); // 2
System.out.println(redisTemplate.opsForSet().members("unionAndStoreTest")); // [aaa, bbb]
```

- Long unionAndStore(K key, Collection<K> otherKeys, K destKey);
key无序集合与多个otherkey无序集合的并集存储到destKey无序集合中


- Set<V> difference(K key, K otherKey);
key无序集合与otherKey无序集合的差集 (并集 - 交集) -> 注：有顺序 (第一个有，第二个没有)

```
System.out.println(redisTemplate.opsForSet().members("setTest")); // [aaa]
System.out.println(redisTemplate.opsForSet().members("setTest2"));// [aaa, bbb]
System.out.println(redisTemplate.opsForSet().difference("setTest2","setTest")); // [bbb]
System.out.println(redisTemplate.opsForSet().difference("setTest","setTest2")); // []
```

- Set<V> difference(K key, Collection<K> otherKeys);
key无序集合与多个otherKey无序集合的差集


- Long differenceAndStore(K key, K otherKey, K destKey);
key无序集合与otherkey无序集合的差集存储到destKey无序集合中

```
System.out.println(redisTemplate.opsForSet().members("setTest")); // [aaa]
System.out.println(redisTemplate.opsForSet().members("setTest2"));// [aaa, bbb]
System.out.println(redisTemplate.opsForSet().differenceAndStore("setTest2","setTest","differenceAndStore")); // 1
System.out.println(redisTemplate.opsForSet().members("differenceAndStore")); // [bbb]
```

- Long differenceAndStore(K key, Collection<K> otherKeys, K destKey);
key无序集合与多个otherkey无序集合的差集存储到destKey无序集合中

- V randomMember(K key);
随机获取key无序集合中的一个元素

```
System.out.println(redisTemplate.opsForSet().randomMember("setTest")); // bbb
System.out.println(redisTemplate.opsForSet().randomMember("setTest")); // aaa
```

- List<V> randomMembers(K key, long count);
随机获取多个key无序集合中的元素，count表示个数

```
redisTemplate.delete("setTest");
redisTemplate.opsForSet().add("setTest", "a","b","c","d","e");
System.out.println(redisTemplate.opsForSet().randomMembers("setTest",8));//[c, e, e, a, c, b, c, e]
System.out.println(redisTemplate.opsForSet().randomMembers("setTest",4));//[c, c, b, e]
```

- Set<V> distinctRandomMembers(K key, long count);
获取多个key无序集合中的元素（去重），count表示个数

```
System.out.println(redisTemplate.opsForSet().distinctRandomMembers("setTest",6));//[a, e, b, c, d]
System.out.println(redisTemplate.opsForSet().distinctRandomMembers("setTest",4));// [d, a, c, e]
```

- Cursor<V> scan(K key, ScanOptions options);
遍历set

```
Cursor<Object> curosr = redisTemplate.opsForSet().scan("setTest", ScanOptions.NONE);
    while(curosr.hasNext()){
        System.out.println(curosr.next());
        //a
        //d
        //c
        //e
        //b
    }
```

## Redis的ZSet数据结构
Redis**有序集合**和无序集合一样也是string类型元素的集合,且**不允许重复**的成员。
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
有序集合的成员是唯一的,但分数(score)却可以重复。
public interface ZSetOperations<K,V>
ZSetOperations提供了一系列方法对有序集合进行操作

- Boolean add(K key, V value, double score);
新增一个有序集合，存在的话为false，不存在的话为true

```
System.out.println(redisTemplate.opsForZSet().add("zset1","zset-1",1.0)); // true
```

- Long add(K key, Set<TypedTuple<V>> tuples);
新增一个有序集合

```
ZSetOperations.TypedTuple<Object> objectTypedTuple1 = new DefaultTypedTuple<Object>("zset-5",9.6);
ZSetOperations.TypedTuple<Object> objectTypedTuple2 = new DefaultTypedTuple<Object>("zset-6",9.9);
Set<ZSetOperations.TypedTuple<Object>> tuples = new HashSet<ZSetOperations.TypedTuple<Object>>();
tuples.add(objectTypedTuple1);
tuples.add(objectTypedTuple2);
System.out.println(redisTemplate.opsForZSet().add("zset1",tuples)); // 2
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, zset-5, zset-6]
```

- Set<V> range(K key, long start, long end);
通过索引区间返回有序集合成指定区间内的成员，其中有序集成员按分数值递增(从小到大)顺序排列

```
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, zset-5, zset-6]
```

- Set<TypedTuple<V>> rangeWithScores(K key, long start, long end);
通过索引区间返回有序集合成指定区间内的成员对象，其中有序集成员按分数值递增(从小到大)顺序排列

```
Set<ZSetOperations.TypedTuple<Object>> tuples1 = redisTemplate.opsForZSet().rangeWithScores("zset1",0,-1);
Iterator<ZSetOperations.TypedTuple<Object>> iterator = tuples1.iterator();
while (iterator.hasNext())
{
    ZSetOperations.TypedTuple<Object> typedTuple = iterator.next();
    System.out.println("value:" + typedTuple.getValue() + "；score:" + typedTuple.getScore());
    //value:zset-1；score:1.0
    //value:zset-5；score:9.6
    //value:zset-6；score:9.9
}

```

- Set<V> rangeByScore(K key, double min, double max);
通过分数返回有序集合指定区间内的成员，其中有序集成员按分数值递增(从小到大)顺序排列

```
System.out.println(redisTemplate.opsForZSet().rangeByScore("zset1",0,5)); // [zset-1]
```

- Set<TypedTuple<V>> rangeByScoreWithScores(K key, double min, double max);
通过分数返回有序集合指定区间内的成员对象，其中有序集成员按分数值递增(从小到大)顺序排列

```
Set<ZSetOperations.TypedTuple<Object>> tuples2 = redisTemplate.opsForZSet().rangeByScoreWithScores("zset1",0,5);
Iterator<ZSetOperations.TypedTuple<Object>> iterator1 = tuples2.iterator();
while (iterator1.hasNext())
{
    ZSetOperations.TypedTuple<Object> typedTuple = iterator1.next();
    System.out.println("value:" + typedTuple.getValue() + "；score:" + typedTuple.getScore()); // value:zset-1；score:1.0
}
```

- Set<V> rangeByScore(K key, double min, double max, long offset, long count);
通过分数返回有序集合指定区间内的成员，并在索引范围内，其中有序集成员按分数值递增(从小到大)顺序排列

```
redisTemplate.opsForZSet().add("zset1","set-2",1.2);
redisTemplate.opsForZSet().add("zset1","zset-3",2.3);
redisTemplate.opsForZSet().add("zset1","zset-4",6.6);
System.out.println(redisTemplate.opsForZSet().rangeByScore("zset1",0,5)); // [zset-1, set-2, zset-3]
System.out.println(redisTemplate.opsForZSet().rangeByScore("zset1",0,5,2,1)); // [zset-3]
```

- Set<TypedTuple<V>> rangeByScoreWithScores(K key, double min, double max, long offset, long count);
通过分数返回有序集合指定区间内的成员对象，并在索引范围内，其中有序集成员按分数值递增(从小到大)顺序排列

```
Set<ZSetOperations.TypedTuple<Object>> tuples3 = redisTemplate.opsForZSet().rangeByScoreWithScores("zset1", 0, 5, 1, 2);
Iterator<ZSetOperations.TypedTuple<Object>> iterator3 = tuples3.iterator();
while (iterator3.hasNext()) {
    ZSetOperations.TypedTuple<Object> typedTuple = iterator3.next();
    System.out.println("value:" + typedTuple.getValue() + "；score:" + typedTuple.getScore());
    // value:set-2；score:1.2
    // value:zset-3；score:2.3
}
```

Set<V> reverseRange(K key, long start, long end);
通过索引区间返回有序集合成指定区间内的成员，其中有序集成员按分数值递减(从大到小)顺序排列

```
System.out.println(redisTemplate.opsForZSet().reverseRange("zset1",0,-1)); // [zset-6, zset-5, zset-4, zset-3, set-2, zset-1]
```


Set<TypedTuple<V>> reverseRangeWithScores(K key, long start, long end);
通过索引区间返回有序集合成指定区间内的成员对象，其中有序集成员按分数值递减(从大到小)顺序排列


Set<V> reverseRangeByScore(K key, double min, double max);
与rangeByScore调用方法一样，其中有序集成员按分数值递减(从大到小)顺序排列


Set<TypedTuple<V>> reverseRangeByScoreWithScores(K key, double min, double max);
与rangeByScoreWithScores调用方法一样，其中有序集成员按分数值递减(从大到小)顺序排列

Set<V> reverseRangeByScore(K key, double min, double max, long offset, long count);
与rangeByScore调用方法一样，其中有序集成员按分数值递减(从大到小)顺序排列

Set<TypedTuple<V>> reverseRangeByScoreWithScores(K key, double min, double max, long offset, long count);
与rangeByScoreWithScores调用方法一样，其中有序集成员按分数值递减(从大到小)顺序排列


- Long rank(K key, Object o);
返回有序集中指定成员的排名，其中有序集成员按分数值递增(从小到大)顺序排列

```
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, set-2, zset-3, zset-4, zset-5, zset-6]
System.out.println(redisTemplate.opsForZSet().rank("zset1","zset-3")); // 2
System.out.println(redisTemplate.opsForZSet().rank("zset1","zset-7")); // null  若无该Object，返回null
```

- Long reverseRank(K key, Object o);
返回有序集中指定成员的排名，其中有序集成员按分数值递减(从大到小)顺序排列

```
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, set-2, zset-3, zset-4, zset-5, zset-6]
System.out.println(redisTemplate.opsForZSet().reverseRank("zset1","zset-3")); // 3
System.out.println(redisTemplate.opsForZSet().reverseRank("zset1","zset-7")); // null  若无该Object，返回null
```

Long count(K key, double min, double max);
通过分数返回有序集合指定区间内的成员个数

```
System.out.println(redisTemplate.opsForZSet().rangeByScore("zset1",0,5)); // [zset-1, set-2, zset-3]
System.out.println(redisTemplate.opsForZSet().count("zset1",0,5)); // 3

```

Long size(K key);
获取有序集合的成员数，内部调用的就是zCard方法

```
System.out.println(redisTemplate.opsForZSet().size("zset1")); // 6
```

Long zCard(K key);
获取有序集合的成员数

```
System.out.println(redisTemplate.opsForZSet().zCard("zset1")); // 6
```

Double score(K key, Object o);
获取指定成员的score值

```
System.out.println(redisTemplate.opsForZSet().score("zset1","zset-1")); // 1.0
```

Long remove(K key, Object... values);
从有序集合中移除一个或者多个元素

```
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, set-2, zset-3, zset-4, zset-5, zset-6]
System.out.println(redisTemplate.opsForZSet().remove("zset1","zset-6")); // 1
System.out.println(redisTemplate.opsForZSet().range("zset1",0,-1)); // [zset-1, set-2, zset-3, zset-4, zset-5]
```

Long removeRange(K key, long start, long end);
移除指定索引位置的成员，其中有序集成员按分数值递增(从小到大)顺序排列

```
System.out.println(redisTemplate.opsForZSet().add("zset2","zset-1",1.1)); // true
System.out.println(redisTemplate.opsForZSet().add("zset2","zset-2",1.2)); // true
System.out.println(redisTemplate.opsForZSet().add("zset2","zset-3",2.3)); // true
System.out.println(redisTemplate.opsForZSet().add("zset2","zset-4",6.6)); // true

System.out.println(redisTemplate.opsForZSet().range("zset2",0,-1)); // [zset-1, zset-2, zset-3, zset-4]
System.out.println(redisTemplate.opsForZSet().removeRange("zset2",1,2)); // 2
System.out.println(redisTemplate.opsForZSet().range("zset2",0,-1)); // [zset-1, zset-4]
```

Long removeRangeByScore(K key, double min, double max);
根据指定的score值得范围来移除成员

```
System.out.println(redisTemplate.opsForZSet().range("zset2",0,-1)); // [zset-1, zset-4]
System.out.println(redisTemplate.opsForZSet().removeRangeByScore("zset2",1,3)); // 1
System.out.println(redisTemplate.opsForZSet().range("zset2",0,-1)); //  [zset-4]
```

Long unionAndStore(K key, K otherKey, K destKey);
计算给定的一个有序集的并集，并存储在新的 destKey中，key相同的话会把score值相加

```
System.out.println(redisTemplate.opsForZSet().add("zzset1","zset-1",1.0));
System.out.println(redisTemplate.opsForZSet().add("zzset1","zset-2",2.0));
System.out.println(redisTemplate.opsForZSet().add("zzset1","zset-3",3.0));
System.out.println(redisTemplate.opsForZSet().add("zzset1","zset-4",6.0));

System.out.println(redisTemplate.opsForZSet().add("zzset2","zset-1",1.0));
System.out.println(redisTemplate.opsForZSet().add("zzset2","zset-2",2.0));
System.out.println(redisTemplate.opsForZSet().add("zzset2","zset-3",3.0));
System.out.println(redisTemplate.opsForZSet().add("zzset2","zset-4",6.0));
System.out.println(redisTemplate.opsForZSet().add("zzset2","zset-5",7.0));
System.out.println(redisTemplate.opsForZSet().unionAndStore("zzset1","zzset2","destZset11")); // 5

Set<ZSetOperations.TypedTuple<Object>> tuples4 = redisTemplate.opsForZSet().rangeWithScores("destZset11",0,-1);
Iterator<ZSetOperations.TypedTuple<Object>> iterator2 = tuples4.iterator();
while (iterator2.hasNext())
{
    ZSetOperations.TypedTuple<Object> typedTuple = iterator2.next();
    System.out.println("value:" + typedTuple.getValue() + "；score:" + typedTuple.getScore());
    // value:zset-1；score:2.0
    //value:zset-2；score:4.0
    //value:zset-3；score:6.0
    //value:zset-5；score:7.0
    //value:zset-4；score:12.0
}

```


Long unionAndStore(K key, Collection<K> otherKeys, K destKey);
计算给定的多个有序集的并集，并存储在新的 destKey中


Long intersectAndStore(K key, K otherKey, K destKey);
计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中

```
System.out.println(redisTemplate.opsForZSet().intersectAndStore("zzset1","zzset2","destZset33")); // 4
Set<ZSetOperations.TypedTuple<Object>> tuples5 = redisTemplate.opsForZSet().rangeWithScores("destZset33",0,-1);
Iterator<ZSetOperations.TypedTuple<Object>> iterator4 = tuples5.iterator();
while (iterator4.hasNext())
{
    ZSetOperations.TypedTuple<Object> typedTuple = iterator4.next();
    System.out.println("value:" + typedTuple.getValue() + "score:" + typedTuple.getScore());
    // value:zset-1score:2.0
    //value:zset-2score:4.0
    //value:zset-3score:6.0
    //value:zset-4score:12.0
}
```

Long intersectAndStore(K key, Collection<K> otherKeys, K destKey);
计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中


Cursor<TypedTuple<V>> scan(K key, ScanOptions options);
遍历zset

```
Cursor<ZSetOperations.TypedTuple<Object>> cursor = redisTemplate.opsForZSet().scan("zzset1", ScanOptions.NONE);
while (cursor.hasNext()){
    ZSetOperations.TypedTuple<Object> item = cursor.next();
    System.out.println(item.getValue() + ":" + item.getScore());
    // zset-1:1.0
    //zset-2:2.0
    //zset-3:3.0
    //zset-4:6.0
}
```

Double incrementScore(K key, V value, double delta);
增加元素的score值，并返回增加后的值

```
System.out.println(redisTemplate.opsForZSet().score("zset1","zset-1")); // 1.0
System.out.println(redisTemplate.opsForZSet().incrementScore("zset1","zset-1",1.1));  // 2.1
System.out.println(redisTemplate.opsForZSet().score("zset1","zset-1")); // 2.1
```

