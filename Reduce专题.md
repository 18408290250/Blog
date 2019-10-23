# reduce专题

签名：
```
Optional<T> reduce(BinaryOperator<T> accumulator);
T reduce(T identity, BinaryOperator<T> accumulator);
<U> U reduce(U identity,
                 BiFunction<U, ? super T, U> accumulator,
                 BinaryOperator<U> combiner);
```



## 第一个签名

```
Optional<T> reduce(BinaryOperator<T> accumulator);
```
- 接受一个BinaryOperator类型的lambada表达式
lambada表达式的a参数是表达式的执行结果的缓存，
也就是表达式这一次的执行结果会被作为下一次执行的参数，
而第二个参数b则是依次为stream中每个元素。
如果表达式是第一次被执行，a则是stream中的第一个元素

- 表达式被调用了5次， 第一次a和b分别为stream的第一和第二个元素，因为第一次没有中间结果可以传递， 所以 reduce方法实现为直接将第一个元素作为中间结果传递

```
//  返回Optional，允许列表中有null值，在计算的时候用Optional包装
    List<Integer> numList = Arrays.asList(1,2,3,4,5, null);
    Optional<Integer> reduce = numList.stream().reduce((a, b) -> {
        System.out.println("a=" + a + ",b=" + b);
        return Optional.ofNullable(a).orElse(0) + Optional.ofNullable(b).orElse(0);
    });
    System.out.println(reduce.orElse(0));


a=1,b=2
a=3,b=3
a=6,b=4
a=10,b=5
a=15,b=null
15
result=0
```



- 流为空时，第一种实现并不直接方法计算的结果，而是将计算结果用Optional来包装

```
 // 第一种签名，流为空时，不会出现返回结果为null，而是一个Optional
	List<Integer> numList = Arrays.asList();
	Optional<Integer> reduce = numList.stream().reduce((a, b) -> {
	    System.out.println("a=" + a + ",b=" + b);
	    return a+b;
	});
	System.out.println(reduce);  // Optional.empty

```



## 第二个签名

```
T reduce(T identity, BinaryOperator<T> accumulator);
```
- 与第一个签名的实现的区别是它首次执行时表达式第一次参数并不是stream的第一个元素，而是通过签名的第一个参数identity来指定


```
	List<Integer> numList1 = Arrays.asList(1,2,3,4,5);
//  int result = numList1.stream().reduce(0,(a,b) ->  a + b );
    int result = numList1.stream().reduce(0,(a,b) ->  {
        System.out.println("a="+a+",b="+b);
        return a+b;
    } );
    System.out.println("result="+result);
}

a=0,b=1
a=1,b=2
a=3,b=3
a=6,b=4
a=10,b=5
result=15

```


- 允许指定初始值，因此即使stream为空，也不会出现返回结果为null的情况，当stream为空，reduce为直接把初始值返回


```
   List<Integer> numList1 = Arrays.asList();
//      int result = numList1.stream().reduce(0,(a,b) ->  a + b );
    int result = numList1.stream().reduce(0,(a,b) ->  {
        System.out.println("a="+a+",b="+b);
        return a+b;
    } );
    System.out.println("result="+result); // result=0
}


```



## 第三种签名

第三种签名的用法相较前两种稍显复杂，前两种实现有一个缺陷，它们的计算结果必须和stream中的元素类型相同.stream中的类型为int，那么计算结果也必须为int，这导致了灵活性的不足，甚至无法完成某些任务。

比入我们要对一个一系列int值求和，但是求和的结果用一个int类型已经放不下，必须升级为long类型，此实第三签名就能发挥价值了，它不将执行结果与stream中元素的类型绑死。



// 并行流的时候，初始值会被每个线程拿到，再原来的结果上增加值
```
 System.out.println(IntStream.of(1,2,3).parallel().sum());  // 6
 System.out.println(IntStream.of(1, 2, 3).reduce(1, Integer::sum));  // 7 初始值为1  1+1+2+3
 System.out.println(IntStream.of(1, 2, 3).parallel().reduce((x, y) -> x + y).orElse(1)); // 6
 System.out.println(IntStream.of(1, 2, 3).parallel().reduce(1, Integer::sum));  // 9  每个线程拿到初始值为1 1*3+1+2+3
```


```
String str1 = new String("abc");
String str2 = "abc";
String str3 = "ab" + new String("c");
String str4 = "abc";


```

