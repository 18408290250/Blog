```

Optional<String> company1 = null;
Optional<String> company2 = Optional.ofNullable(null);

//  若元素为Optional类型，则如果需要传参null值，则应传Optional.empty
//  因为此时调用optional的orElse方法，若为null.orElse（）会错
System.out.println(company1.orElse(new String("123")));
System.out.println(company2.orElse(new String("123")));

// 若元素为其余Object类型，值为null时，想用orElse方法；则应在调用之前转为Optiona.
// Optional.ofNullable(null) ->  Optional.empty()

System.out.println(Optional.empty().orElse(new String("123")));
System.out.println(Optional.ofNullable(null).orElse(new String("123")));

```