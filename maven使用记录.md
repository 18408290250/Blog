idea maven打包：

```
mvn clean package -Dmaven.test.skip=true
```





maven本地仓库安装：

```
mvn install:install-file -Dfile=E:\fadada_api_sdk_2.4.0.jar  -DgroupId=com.fadada -DartifactId=fadada_api_sdk -Dversion=2.4.0 -Dpackaging=jar
```



```
<dependency>
      <groupId>com.fadada</groupId>
          <artifactId>fadada_api_sdk</artifactId>
      <version>2.4.0</version>
</dependency>
```



