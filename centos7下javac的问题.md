在centos7的terminal中输入java -verison是没问题的，但是javac的话就会提示没有此命令

百度了好久，说的很复杂，照着做了，最后也没有成功

后来在stackoverflow上看到了这个

84 down vote accepted
You installed the Java Runtime Environment (JRE) only, which does not contain javac. For javac, you have to install the OpenJDK Development Environment. You can install java-devel or java-1.6.0-openjdk-devel, which both include javac.

By the way: you can find out which package provides javac with a yum search, e.g.

su -c 'yum provides javac'
Another note: using yum and openjdk is only one possibility to install the JDK. Many people prefer Sun/Oracle's "original" SDK. See How to install Java SDK on CentOS? and links for alternatives.



大意就是我们用yum来装原生的就行了

使用

```
yum install java-devel
```

