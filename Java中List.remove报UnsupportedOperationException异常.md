Java中List.remove报UnsupportedOperationException异常

想到了List中提供的removeAll()方法可以求得差集，但是结果确报了UnsupportedOperationException异常。

仔细分析了下，List A我是通过数组经过Arrays.asList()转化成List，但是它是继承AbstractList的子类，而AbstractList是不支持removeAll()和remove()操作的。

**list.add(6),此ArrayList非我们常用的ArrayList，我们常用的ArrayList是java.util.ArrayList，而这里new的是java.util.Arrays.ArrayList**



如果要执行remove()和removeAll()操作，可以转化成Set()或者ArrayList、LinkedList才可以。

因为我的需求List A中的元素要保证唯一，所以我在数组转化成List之后又转化成了Set，之后就没问题了

```
List<String> stringList = new ArrayList<>(Arrays.asList("this", "is", "a", "list", "of", "strings"));

```