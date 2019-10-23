# 直接用端口号访问项目，tomcat配置

server.xml添加：

```
<Context path="" docBase="/usr/local/tool/cvs-front-social/apache-tomcat-7.0.77/webapps/helloword" debug="0" reloadable="true"/>

```



杀掉进程

```
#看是否已经有tomcat在运行了
ps -ef |grep tomcat
#如果有，用kill;
kill -9 pid #pid 为相应的进程号

```
