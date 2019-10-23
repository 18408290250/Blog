java.util.function中的接口分为4类：
Consumer(消费型接口) 传入一个泛型参数，不返回任何值
Predicate(谓词型接口) 传入一个参数，返回一个布尔值
Supplier(供给型) 不传入任何参数，返回一个值
Function(功能型接口) 传入一个参数，返回一个值



## Consumer

连续调用(andThen)
```
   void accept(T t);

  default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }

```
常见实现：
forEach

其他Consumer接口：
IntConsumer  void accept(int x)
Double……
Long……
BiConsumer   void accept(T t,U u)


```
lisiList.stream() .forEach(System.out::println);

```

```
  Map<String, String> map = new HashMap<>();
        map.put("a", "a");
        map.put("b", "b");
        map.put("c", "c");
        map.put("d", "d");
        map.forEach((k, v) -> {
            System.out.println(k + " " + v + " ");
        });
```

```
List<String> stringList = new ArrayList<>(Arrays.asList("this", "is", "a", "list", "of", "strings"));

        List<String> lisiList = new ArrayList<>();

		// 先给lisiList加入 this
        Consumer<String> consumer = x -> {
            if (x.startsWith("th")) {   // x代表stringList集合中的每一个元素，消费数据：stringList
                lisiList.add(x);
            }
        };

	    // 再给lisiList加入整个stringList
        consumer = consumer.andThen(
                x -> lisiList.add(x)  // x代表stringList集合中的每一个元素  消费数据：stringList
        );

        stringList.stream()
                .forEach(consumer);

        lisiList.stream()
                .forEach(System.out::println);  // this this is a list of strings
```



## Supplier

```
 T get();
```

……

