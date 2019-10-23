## Lambda表达式
Lambda表达式是一个能够作为参数传递的匿名函数对象，它没有名字，有参数列表、函数体、返回类型，也可以抛出异常。它的类型是函数接口（Functional Interface）。

Lambda表达式是函数式接口的实例对象

语法：　　
　　1)  形式参数:即参数列表，就是函数式接口里方法的参数。
　　2) 向右的箭头（->）：起分割作用。
　　3)  方法体。表达式或者代码块，是函数式接口中方法的实现。（可以return返回值）
　　

## 函数式接口和Lambda表达式的联系

Lambda表达式需要一个函数式接口作为其对应类型，而它的方法体就是函数接口的实现。每一个该接口类型的Lambda表达式都会被匹配到该接口的抽象方法。



## lambda表达式

 将结果赋给函数式接口类型的引用

使用某种lambda表达式语法，并将结果赋给函数式接口类型的引用
函数式接口是一种包含单一抽象方法的接口。类通过为接口中的所有方法提供实现来实现任何接口
,可以通过顶级类、内部类、匿名内部类来实现

方法签名兼容： lamda表达式必须匹配接口中单一抽象方法签名的参数类型和返回类型
lamdba表达式属于接口中方法的实现，可以将其赋给该接口类型的引用

实现方法的名称并不重要，不会作为lamdba表达式的一部分。


**将lamdba表达式赋给函数式接口:
java库中不存在名为Lambda的类，lambda表达式只能被赋给函数式接口引用**

lamdba表达式属于函数式接口中单一抽象方法的实现
lamdba表达式视为实现接口的匿名内部类的主体

lambda表达式既可以是方法的参数，也可以是方法的返回类型，还可以被赋给引用。但赋值类型必须是函数式接口。

```
	 // 匿名内部类
     File directory = new File("");
     String[] s_names =  directory.list(new FilenameFilter() {
         @Override
         public boolean accept(File dir, String name) {
             return name.endsWith(".java");
         }
     });

     // lambda表达式实现
     String[] s_names1 = directory.list((dir,name)->  name.endsWith(".java") );

     // 显示数据类型
     directory.list((File dir,String name)-> name.endsWith(".java"));

     // lambda表达式的实现多于一行，则需要使用大括号和显示返回语句return
     directory.list((dir,name)->  { return name.endsWith(".java"); });
```



## 方法引用

将现有方法作为lambda表达式进行处理，将实例引用或类名与方法分开   本质属于lambda表达式的一种简化语法
lambda本质上是将方法作为对象进行处理；    

方法引用-将现有方法作为lambda表达式进行处理

方法引用有以下三种形式
- object::instanceMethod
引用特定对象的实例方法，如：System.out::printLn
- Class::staticMethod
引用静态方法，如Math::max
-Class::instanceMethod
调用特定类型的任意对象的实例方法，如String.length

lambda表达式和方法引用在任何情况都不能脱离上下文存在。以对象引用为例，上下文提供了方法的参数。
```
     // 相当于 System.out::println
     // 上下文提供了x的值，它被用作方法的参数
     x->System.out.println(x)

     // 相当于 Math::max
     // 上下文需要提供两个参数，lambda表达式返回较大的参数
     (x,y) -> Math.max(x,y)

     // 相当于 String::length
     // 上下文提供x的值，它将用作方法的目标而非参数
     x -> x.length()

```
 通过类名引用一个传入多个参数的方法，则上下文提供的第一个元素将作为方法的目标，其他元素作为方法的参数

```

     // 上下文是两个字符串，方法引用语法调用第一个元素(lambda表达式中的s1)的compareTo方法，并使用第二个元素s2作为该方法的参数
     List<String> stringList = Arrays.asList("this", "is", "a", "list", "of", "strings");
     List<String> collect = stringList.stream()
             .sorted((s1, s2) -> s1.compareTo(s2))
             .collect(Collectors.toList());

     List<String> collect1 = stringList.stream()
             .sorted(String::compareTo)
             .collect(Collectors.toList());
```



## 构造函数引用——在方法引用中使用new关键字

将人员列表转化为姓名列表
1.直接获取姓名并创建集合
```

     List<String> collect3 = employees.stream()
             .map(person -> person.getName())
             .collect(Collectors.toList());

     List<String> collect4 = employees.stream()
             .map(Employee::getName)
             .collect(Collectors.toList());


```
使用lambda表达式来调用构造方法
2. 根据字符串来创建(new)对应的引用列表

```
     employees.stream()
              .map(Employee::getName)
             .map(name -> new Employee_Name(name))
             .collect(Collectors.toList())
             .forEach(System.out::println);

```
使用构造函数引用来实例化Employee_Name，Employee_Name::new的作用是引用Employee_Name类的构造方法,由上下文(**流里面是什么东西**)决定执行哪一个构造方法

由于上下文提供了一个字符串，使用单参数的String构造函数

```
     employees.stream()
             .map(Employee::getName)
             .map(Employee_Name::new)
             .collect(Collectors.toList())
             .forEach(System.out::println);

     employees.stream()
             .map(Employee::getName)
             .map(Employee::new)
             .collect(Collectors.toList())
             .forEach(System.out::println);
```
多个属性构造只能采用lamdba表达式的形式来写
```
     employees.stream()
             .map((x)-> new Employee(x.getId(),x.getName()))
             .collect(Collectors.toList())
             .forEach(System.out::println);

```

## 复制构造函数

人员列表先转化为流，流再转化为列表，引用不会发生变化，对象相同。

```
    public Employee(Employee e) {
        this.id = e.id;
        this.name = e.name;
    }

```
列表转化为流，再转化为列表,对象相同
```
     List<Employee> collect2 =
             Stream.of(employees.get(0))
             .collect(Collectors.toList());
     System.out.println(collect2.get(0) == employees.get(0)); // true


```
当调用map方法，上下文是Employee实例的流。
Employee::new 调用参数是Employee的构造方法，传入一个Employee实例并返回一个等效的新实例
```
     List<Employee> collect7 =
             Stream.of(employees.get(0))
             .map(Employee::new)
             .collect(Collectors.toList());
     System.out.println(collect7.get(0) == employees.get(0)); // false
```



## 可变参数构造函数  

 将列表中的每个字符串拆分为每个单词，并调用可变参数构造函数  参数实际为：String[] 

```
    public Employee(String ... names){
        this.name = Arrays.stream(names).
                collect(Collectors.joining(" "));
        System.out.println("Varargs stor,names="+Arrays.asList(names));
    }


     List<String> names_1 = Arrays.asList("Amy BoB", "Tom");
     names_1.stream()
             .map(name -> name.split(" "))
             .map(Employee::new)
             .collect(Collectors.toList())
             .forEach(System.out::println);

     //Varargs stor,names=[Amy, BoB]
     //Varargs stor,names=[Tom]
     //Employee{id=0, name='Amy BoB'}
     //Employee{id=0, name='Tom'}
```

## 数组

构造函数引用也可以和数组一起使用，如果希望采用对象的实例数组(Employee[])而非列表，可以使用Stream接口定义的toArray方法
```
<A> A[] toArray(IntFunction<A[]> generator)

// 采用A表示返回数组的泛型类型，数组包含流的元素，由所提供的generator函数创建，甚至可以使用构造函数引用
```

将列表转为数组

toArray方法参数创建了一个大小合适的Employee引用数组，并采用经过实例化的Employee实例进行填充

```
List<Employee> employees = Arrays.asList(
             (new Employee(1,"aa")),
             (new Employee(2,"bb")),
             (new Employee(3,"dd")),
             (new Employee(4,"cc")));

 Employee[] employees1 = 
		  employees.stream()
         .map(Employee::new) // Employee的构造函数引用（参数为Employee，因为流的上下文是Employee）
         .toArray(Employee[]::new);  // Employee数组的构造函数引用

 Stream.of(employees1).forEach(System.out::println);


```
列表也直接有toArray方法
```
 Object[] objects =  employees.toArray();
 Stream.of(objects).
         map(x -> (Employee)x)
         .forEach(System.out::println);
```

## 函数式接口

使用现在已有的函数式接口，或编写自定义函数式接口
只包含**单一抽象方法**的接口，并为其添加@FunctionalInterfacde注解，abstract可省略，访问修饰符public也可省略
**函数式接口可以使用default和static方法，由于这两类方法都有实现**，它们与“仅包含一个抽象方法”并不矛盾

函数式接口因为只包含单一抽象方法的接口，所以可以作为lambda表达式或方法引用的目标
**Object类中的方法与单一抽象方法可以共存**。例如：Comparator接口中有抽象方法compare(T o1,T o2)和来自Object类的equals方法。



### 接口中的默认方法

为保持向后兼容，Java的许多现有接口都采用默认方法进行增强，
一般而言，为接口添加新方法会破坏现有的方法。如果添加的新方法被声明为默认方法，则所有现有的实现将继承新方法且仍然有效。这使得库维护者可以在JDK中添加新的默认方法，而不会破坏现有的实现。

java之所以不支持多继承，是为了避免所谓的钻石问题(菱形继承问题)。（多重继承：一个类继承多个类的相同方法，拥有多份，……），由于接口只包含抽象方法，不会存在相互冲突的实现。**接口之所以允许多继承，是因为只有方法签名被继承**，只有一份，方法实现需对象自己重写。

默认方法冲突：
- 类的方法与接口的默认方法冲突，类方法优先
- 如果两个接口（其中一个接口是另一个接口的后代）发生冲突，则后代接口优先；
  如果两个类（其中一个类是另一个类的后代）发生冲突，则后代类优先
- 如果两个默认方法之间不存在继承关系，则无法编译（钻石问题）


```
public interface Is  {
 default  void test(){
     System.out.println("IS");
 }
}

public interface Is_a extends Is {
    default  void test(){
        System.out.println("IS_a");
    }
}

public class AA implements Is,Is_a {
//
//    @Override
//    public void test() {
//        System.out.println("AA");
//    }
    public static void main(String[] args) {
        AA aa = new AA();
        aa.test();
    }
}
```


###接口中的静态方法
Java中的静态成员是类级别的，静态成员与整个类相关联，而不与特定的实例关联

类的静态方法通过类名调用；接口的静态方法通过接口名调用。
```
AA.static_function();
Is.static_function();
```


Java8之前，接口不支持静态成员 =》 工具类（只包含静态方法的类）
接口中的静态方法：
- static关键字
- 静态方法必须有实现
- 无法重写静态方法
- 通过接口名访问静态方法
- 无须实现接口调用静态方法



字符串排序：Comparator接口中的静态方法comparingInt、comparingLong、comparingDouble、naturalOrder、reverseOrder

```
	List<String> bonds = Arrays.asList("Connery","Lazenby","Moore","Dalton","Brosnan","Craig");
     // 自然顺序排序（字典顺序）
     bonds.stream()
             .sorted(Comparator.naturalOrder())
             .collect(Collectors.toList())
             .forEach(System.out::println);

     // 反向顺序（字典顺序)
     bonds.stream()
             .sorted(Comparator.reverseOrder())
             .collect(Collectors.toList())
             .forEach(System.out::println);

     // 按小写名称排序
    bonds.stream()
            .sorted(Comparator.comparing(String::toLowerCase))
            .collect(Collectors.toList())
            .forEach(System.out::println);

     // 按大写名称排序
     bonds.stream()
             .sorted(Comparator.comparing(String::toUpperCase))
             .collect(Collectors.toList())
             .forEach(System.out::println);

     // 按姓名长度排序
     bonds.stream()
             .sorted(Comparator.comparingInt(String::length))
             .collect(Collectors.toList())
             .forEach(System.out::println);

     // 按姓名长度排序，若一致，按字典序排序
     bonds.stream()
             .sorted(Comparator.comparingInt(String::length)
                                .thenComparing(Comparator.naturalOrder()))
             .collect(Collectors.toList())
             .forEach(System.out::println);
```


并行流：Fork/Join 框架
https://www.cnblogs.com/zhaobingqing/p/7217781.html



