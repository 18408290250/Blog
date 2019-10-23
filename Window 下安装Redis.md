## redis服务端的安装
1.下载windows版本的Redis可以在gitthb上下载
windows版本下载地址：https://github.com/MSOpenTech/redis/tags

2.在git官网下载地址上选择系统配置为：
Redis-x64-3.2.100
win10 64
格式为taz压缩包
3.安装Reidis
解压缩redis压缩包到指定目录
打开cmd命令窗口。将目录指向解压的redis目录
4.启动命令
redis-server redis.windows.conf

出现redis的图形，即表示启动成功
5.设置redis为windows服务
redis-server --service-install redis.windows-service.conf --loglevel verbose

6.启动服务(服务端，也可以用windows服务自动启动)
redis-server --service-start

利用redis-cli而不需要每次进入redis的安装目录，否则每次都需要进入到redis目录

系统变量Path添加压缩包路径 即可

测试redis服务(客户端)
redis-cli
输入：ping ，输出pong，证明服务启动正常

7.如果修改redis数据库的密码和可连接的ip，可以在解压目录下的redis.windows.conf文件中修改
bind 127.0.0.1 修改为0.0.0.0
requirepass foobared 修改为requirepass 新密码
还可以修改器对外开放的端口port 6379 修改为指定端口
此步骤可以在步骤4启动命令之前执行，这样启动的redis数据库服务就是带登录密码的


测试：

切换到redis目录下运行

redis-cli.exe -h 127.0.0.1 -p 6379 
设置键值对 set myKey abc

取出键值对 get myKey



8.安装redis图形化工具连接数据库，完成！！！


## redis客户端(Redis Desktop Manager)的安装
客户端：
- Redis Desktop Manager(最新的版本对于Windows收费)
https://redisdesktop.com/pricing#linux-download

免费版本

- 阿里云 Redis VPC实例（数据管理DMS最新发布
https://help.aliyun.com/knowledge_detail/51934.html?spm=a2c4e.11154000.rtdmain.1.59b33f1062O4Ub


其余客户端介绍：
https://yq.aliyun.com/roundtable/50655


- 
https://www.cnblogs.com/zheting/p/7670154.html
https://yq.aliyun.com/roundtable/50655



idea整合springboot+redis：步骤齐全，但未实验
https://www.jianshu.com/p/19628db2e7ef



tips:
idea整合redis 高版本与低版本的配置不同




https://www.baeldung.com/spring-data-redis-tutorial
对应例子：redisdemo1




Redis 安装
网上有很多 Redis 的安装教程，这里就不多说了，只说下 Docker 的安装方法：

Docker 安装运行 Redis

docker run -d -p 6379:6379 redis:4.0.8
如果以后想启动 Redis 服务，打开命令行，输入以下命令即可。

redis-server
