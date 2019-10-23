
如何在java 8中使用流将集合/数组转换为JSONArray

https://cloud.tencent.com/developer/ask/132007



我有一个双数组，我需要将数组转换为使用Java流的JSONArray。我尝试使用forEach（共享可变性），这会导致数据丢失。

```
public static JSONArray arrayToJson(double[] array) throws JSONException{
    JSONArray jsonArray = new JSONArray();

​```
Arrays.stream(array)
     .forEach(jsonArray::put);  

return jsonArray;
​```

}
```





有什么办法可以使用流创建JSONArray？

你的代码工作，但你可以写这样的东西：

```
return Arrays.stream(array)
            .collect(Collector.of(
                          JSONArray::new, //init accumulator
                          JSONArray::put, //processing each element
                          JSONArray::put  //confluence 2 accumulators in parallel execution
                     )
            );
```



再举一个例子（创建一个Stringfrom List<String>）：

```
List<String> list = ...
String str = this.list
                 .stream()
                 .collect(Collector.of(
                    StringBuilder::new,
                    (b ,s) -> b.append(s),
                    (b1, b2) -> b1.append(b2),
                    StringBuilder::toString   //last action of the accumulator (optional)  
                 ));
```



JSONArray不是线程安全的。如果n 正在使用并行流，则应该同步操作。



```
Arrays
    .stream(array)
    .parallel()
    .forEach(e -> {
        synchronized(jsonArray) {
            jsonArray.put(e);
        }
    });
```






collect 三参数：
```
 /**
         * 第一个函数生成一个新的ArrayList（最后返回的也是这个）
         * 第二个函数的第一个参数是前面生成的ArrayList对象，第二个参数是stream中包含的元素，函数体就是把stream中的元素加入ArrayList对象中。
         * 第二个方法被反复调用直到原stream的元素被消费完毕；
         *
         * 第三个函数也是接受两个参数，这两个都是ArrayList类型的，第一个参数是第一个函数创建的list，
         * 第二个参数，由于在并行的时候会创建多个List存储并行时各个线程所计算完的结果，
         * 猜测应该是一个线程对应一个List存储结果，每个线程都会调用第二个函数进行消费当前线程所需要处理的数据，
         * 如果创建了N个线程，那么就会有N个结果，然后这些结果就一个一个传进第三个函数的第二个参数
         *
         * 非并行流不调用第三个参数：
         * 并行：
         * 一、根据collect第一个参数创建一个List实例(最终得到的也是这个)。
         * 一、把stream的原始list数据划分为N块，然后分别交给N个线程。
         * 二、每个线程根据自己得到的那块list数据执行相应操作，每个线程一开始也是使用collect传进来的第一个函数创建新的List实例，然后使用collect的第二个函数消费当前线程需要处理的那一块list原始数据，然后得到结果。
         * 三、等所有线程执行完毕，得到N个结果（每个结果都是list），此时调用N次第三个函数。第三个函数的第一个参数为第一步创建的list，第二个参数则是各个线程执行完毕得到的结果list，并行计算时有多少个结果list就执行多少次。
         * 四、执行完毕后，返回第一步创建的list
         */
//        IntStream stream = IntStream.of(1,2,3,4,5,6);
//        List result = stream.parallel().collect(() -> new ArrayList<>(), (list, item) -> {
//            list.add(item);
//            System.out.println(list+":"+item);
//        }, (list, list2) -> {
//            System.out.println("three:"+list+":"+list2);
//            list.addAll(list2);
//            System.out.println("four:"+list);
//        });


        Random r = new Random();
        ArrayList<Integer> collect = r.ints(5,10,20).parallel().collect(() -> new ArrayList<>(), (list, item) -> {
            list.add(item);
            System.out.println(list + ":" + item);
        }, (list, list2) -> {
            System.out.println("three:" + list + ":" + list2);
            list.addAll(list2);
//            System.out.println("four:" + list);
        });
```



实际例子：

```
  List<Article> list = IArticle.getArticle();

//        jsonArray = new JSONArray();
//        list.forEach(this::convertJsonArr);
//        list.stream().filter(Objects::nonNull).forEach(this::convertJsonArr);

  jsonArray =  list.stream().parallel().filter(Objects::nonNull)
                .collect(JSONArray::new,(arr,article)-> arr.put(convertJSONObj(article)),JSONArray::put);
                
  
  
    public String convertJsonArr(Article x){
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("id",x.getId());
        jsonObject.put("title",x.getTitle());
        jsonObject.put("name",x.getAuthor());
        jsonObject.put("published",x.getPublished());
        jsonObject.put("tag",x.getTag());
        jsonObject.put("info",x.getInfo());
        jsonArray.put(jsonObject);
        return jsonArray.toString();
    }

    public  JSONObject convertJSONObj(Article x){
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("id",x.getId());
        jsonObject.put("title",x.getTitle());
        jsonObject.put("name",x.getAuthor());
        jsonObject.put("published",x.getPublished());
        jsonObject.put("tag",x.getTag());
        jsonObject.put("info",x.getInfo());
        return jsonObject;
    }
           
```

