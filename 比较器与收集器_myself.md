## 利用比较器实现排序

### Stream.sorted

```
 Stream<T> sorted(Comparator<? super T> comparator);
```

- 通过lambda表达式实现Comparator
- 使用Comparator接口定义的某种comparing方法

```
    List<String> list = Arrays.asList("this", "is", "a", "book");
    // 通过lambda表达式
    // Comparator.comparingInt
   list.stream()
//               .sorted((s1,s2) -> s1.length() - s2.length())
           .sorted(Comparator.comparingInt(String::length))
           .collect(Collectors.toList())
           .forEach(System.out::println);
```

### thenComparing方法

需要对多个字段进行排序

```
 // 先按长度排 再按字母顺序排
        list.stream()
                .sorted(Comparator.comparingInt(String::length).thenComparing(Comparator.naturalOrder()))
                .collect(Collectors.toList())
                .forEach(System.out::println);
```

##将流转化为集合

###创建集合 Stream.collect
```
<R, A> R collect(Collector<? super T, A, R> collector);
<R> R collect(Supplier<R> supplier,
                  BiConsumer<R, ? super T> accumulator,
                  BiConsumer<R, R> combiner);
```

.collect(Collectors.toList())
.collect(Collectors.toSet())
.collect(Collectors.joining())
.collect(Collectors.toCollection(ArrayList::new))
```
List<String> list = Arrays.asList("this", "is", "a", "book");
        list.stream()
                .collect(Collectors.toCollection(ArrayList::new))
                .forEach(System.out::println);
```

###Stream.toArray 用于创建对象数组
```
Object[] toArray();
<A> A[] toArray(IntFunction<A[]> generator);

```
```
	Object[] objects = list.stream().toArray();
	Arrays.stream(objects).forEach(System.out::println);
	
	String[] strings = list.stream()
	        .toArray(String[]::new);
	Arrays.stream(objects).forEach(System.out::println);
```


###Collectors.toMap  用于创建Map
```
    String[] keys = {"this", "is", "a", "book"};
    Map<String, Integer> wordCounts = Arrays.stream(keys)
            .collect(Collectors.toMap(Function.identity(), x -> 0));
```

## 将线性集合添加到映射
使用Collectors.toMap
和Function接口定义的identity方法


## 对映射排序
Map接口始终包含一个成为Map.Entry的公共静态内部接口，它表示一个键值对。Map接口定义的entrySet
Map.Entry.comparingByKey()
Map.Entry.comparingByKey(Comparator<? super K> cmp)
value同理
**
comparingByKey 参数为 Comparator，可自定义规则 Comparator.comparing**

```
.sorted(Map.Entry.comparingByKey(Comparator.comparing(Person::getName)))
或  
.sorted(Map.Entry.comparingByKey((x,y) -> y.compareTo(x)))
```

```
    Map<Integer,Integer> map = new HashMap<>();
    // 将Map排序

    // 1. Comparator.comparign里面直接用e.getKry
    List<Person> list = map.entrySet()
            .stream()
            .sorted(Comparator.comparing(e -> e.getKey()))
 //			.sorted((x,y) -> x.getKey() - y.getKey())
            .map(e -> new Person(e.getKey().toString(), e.getValue().toString())).collect(Collectors.toList());

    // 2. Comparator.comparing里面用 Map.Entry::getValue
    List<Person> list2 = map.entrySet()
            .stream()
            .sorted(Comparator.comparing(Map.Entry::getValue)) //
            .map(e -> new Person(e.getKey().toString(), e.getValue().toString())).collect(Collectors.toList());

    // 3.  sort里面直接用Map.Entry.comparingByKey()
    List<Person> list3 = map.entrySet()
            .stream()
            .sorted(Map.Entry.comparingByKey())
            .map(e -> new Person(e.getKey().toString(), e.getValue().toString())).collect(Collectors.toList());


    map.put(11,33);
    map.put(22,22);
    map.put(33,11);
    // 4. Map.Entry.comparingByKey(Funtion接口)
    Map<Person,Person> map1 = new HashMap<>();
    map.entrySet()
            .stream()
//                .sorted()  // error 因为Entry未实现Comparator接口
//                .sorted(Map.Entry.comparingByKey())
//                .sorted(Map.Entry.comparingByKey(Integer::compareTo))
			  // comparingByKey 参数为 Comparator，可自定义规则 Comparator.comparing
//                .sorted(Map.Entry.comparingByKey(Comparator.comparing(Person::getName)))  
//                .sorted(Map.Entry.comparingByKey(Comparator.reverseOrder()))
            .sorted(Map.Entry.comparingByKey((x,y) -> y.compareTo(x)))

            .forEach(e -> System.out.println("k="+e.getKey()+"v="+e.getValue()));
```

## 分区和分组
