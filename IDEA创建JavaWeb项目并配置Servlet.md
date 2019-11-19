# IDEA创建JavaWeb项目并配置servlet



# 1.创建JavaWeb项目

File -> new Project -> 选中Web Application ,然后next ,finish

![image-20191118211438596](images/image-20191118211438596.png)



![image-20191118211757182](images/image-20191118211757182.png)

# 2.创建JavaWeb项目

2.1 在WEB-INF 目录下点击右键，New --> Directory，创建 classes 和 lib 两个目录
classes目录用于存放编译后的class文件，lib用于存放依赖的jar包

**注意：lib 里面放入 tomcat -》 lib 下面的servlet-api.jar包**



![image-20191118212001367](images/image-20191118212001367.png)





2.2 File --> Project Structure...，进入 Project Structure窗口，点击 Modules --> 选中项目“JavaWeb” --> 切换到 Paths 选项卡 --> 勾选 “Use module compile output path”，将 “Output path” 和 “Test output path” 都改为之前创建的classes目录
即将后面编译的class文件默认生成到classes目录下

![image-20191118212041750](images/image-20191118212041750.png)



2.3 点击 Modules --> 选中项目“JavaWeb” --> 切换到 Dependencies 选项卡 --> 点击右边的“+”，选择 “JARs or directories...”，选择创建的lib目录

![image-20191118212155800](images/image-20191118212155800.png)

![image-20191118212236797](images/image-20191118212236797.png)

![image-20191118212352113](images/image-20191118212352113.png)





# 3.配置Tomcat

------

3.1 Run -> Edit Configurations，进入“Run Configurations”窗口，点击"+"-> Tomcat Server -> Local，创建一个新的Tomcat容器

![image-20191118212931822](images/image-20191118212931822.png)



3.2 在"Name"处输入新的服务名，点击“Application server”后面的“Configure...”，弹出Tomcat Server窗口，选择本地安装的Tomcat目录 -> OK

![image-20191118213104982](images/image-20191118213104982.png)



3.3  配置打包，在“Run Configurations”窗口的“Deployment”面板中，点右边的加号，选择Artifact

![image-20191118213147722](images/image-20191118213147722.png)

IDEA会为该项目自动创建一个名为“JavaWeb:war exploded”的打包方式，表示 打包成war包，并且是文件展开性的，输出路径为当前项目下的 out 文件夹，保持默认即可，应用。

![image-20191118213336394](images/image-20191118213336394.png)



# 4.Servlet测试

4.1在src目录下创建servlet类

![image-20191118213702439](images/image-20191118213702439.png)

```
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/testServlet")
public class ServletTest extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws javax.servlet.ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws javax.servlet.ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("Hello Servlet!");
    }
}

```



4.2 启动tomcat

![image-20191118213850774](images/image-20191118213850774.png)



4.3 访问Servlet

![image-20191118214014097](images/image-20191118214014097.png)



本文参考： https://blog.csdn.net/xuefu_78/article/details/72511744 ，在此基础上加以实践并修改了一些步骤