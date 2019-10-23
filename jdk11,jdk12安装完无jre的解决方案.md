# jdk11,jdk12 安装完后无jre的解决办法

jdk11和jdk12在以前版本基础上，改动有点大，安装后默认是没有jre



**解决方法：**
使用命令手动生成jre

```
bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```



演示：
jdk12的安装目录：F:\jdk_install\jdk12

![avatar](images/jdk12.png)