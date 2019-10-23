## webflux从mono或flux中获取值
1. block() 阻塞
2. 转为MonoProcessor,利用peek(),如下
```
        request.session()
                .toProcessor()
                .peek().getAttributes()
                .putIfAbsent("user","zhangjun");
```


MonoProcessor:
-peek():
Returns the value that completed this MonoProcessor. Returns null if the MonoProcessor has not been completed. If the MonoProcessor is completed with an error a RuntimeException that wraps the error is thrown.
Returns:
the value that completed the MonoProcessor, or null if it has not been completed
Throws:
RuntimeException - if the MonoProcessor was completed with an error


-block():
Block the calling thread indefinitely, waiting for the completion of this MonoProcessor. If the MonoProcessor is completed with an error a RuntimeException that wraps the error is thrown.
Overrides:
block in class Mono<O>
Returns:
the value of this MonoProcessor


在传统单机web应用中，一般使用tomcat/jetty等web容器时，用户的session都是由容器管理。浏览器使用cookie中记录sessionId，容器根据sessionId判断用户是否存在会话session。这里的限制是，session存储在web容器中，被单台服务器容器管理