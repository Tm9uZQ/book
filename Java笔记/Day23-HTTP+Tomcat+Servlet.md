#### JavaWeb

- 静态资源：HTML、CSS、JavaScript等。负责页面展现，资源是一成不变的，运行在浏览器
- 动态资源：Servlet、JSP等。负责逻辑处理，资源是变化的，运行在服务器
- Web服务器：负责解析HTTP协议，解析请求数据，并发送响应数据



#### HTTP

- 特点
  - 基于TCP协议：三次握手进行链接，是可靠的
  - 请求-响应模型：一次请求对应一次响应
  - 无状态的协议：对于事务的处理没有记忆能力，导致每次的请求-响应都是独立的
    - 缺点：多次请求间不能共享数据，可使用Cookie、Session来解决
    - 优点：速度快

- HTTP - 请求数据格式

  - 请求行：请求数据的第一行，由三部分组成。其中`GET`表示请求方式，`/`表示请求资源路径，`HTTP/1.1`表示协议版本
  - 请求头：第二行开始，格式为key: value形式
  - 请求体：**POST请求**的最后一部分，与请求头之间有空行隔开，存放请求参数

  ![1627050930378](images/1627050930378.png)

  

- HTTP - 响应数据格式

  - 响应行：响应的数据的第一行，响应行包含三部分内容，分别是`HTTP/1.1`HTTP协议及版本，`200`响应状态码，`OK`状态码描述
  - 响应头：第二行开始，格式为key: value形式
  - 响应体：最后一部分，和响应头之间有一个空行隔开，存放响应数据

  ![1627053710214](images/1627053710214.png)

  

#### Tomcat

- Web服务器是一个应用程序，对HTTP协议的操作进行封装，主要功能是提供网上信息浏览服务
- Tomcat是Apache软件基金会一个核心项目，是一个开源免费的轻量级Web服务器，支持Servlet/JSP少量JavaEE规范

- 启动的过程中，控制台有中文乱码，需要修改`conf/logging.prooperties`中的UTF-8为GBK
- 关闭建议使用`bin\shutdown.bat`或`ctrl+c`的方式正常关闭
- Tomcat默认的端口是8080，要想修改Tomcat启动的端口号，需要修改 `conf/server.xml`



#### Web项目结构

- Maven Web项目结构（开发中的项目）

  ![1627202865978](images/1627202865978.png)



- 开发完成部署的Web项目

  - 开发项目通过执行Maven打包命令package,可以获取到部署的Web项目目录
  - 编译后的Java字节码文件和resources的资源文件，会被放到WEB-INF下的classes目录下
  - pom.xml中依赖坐标对应的jar包，会被放入WEB-INF下的lib目录下

  ![1627202903750](images/1627202903750.png)



#### Servlet

Servlet是JavaWeb最为核心的内容，是JavaEE规范之一，它是Java提供的一门**动态**web资源开发技术，是==处理浏览器的请求，并作出响应的接口==

- 创建Servlet项目流程
  - 创建web项目，在`pom.xml`文件内的project标签内导入Servlet坐标

  ```xml
    <dependencies>
      <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
      <!--
        此处为什么需要添加该标签?
        provided指的是在编译和测试过程中有效,最后生成的war包时不会加入
         因为Tomcat的lib目录中已经有servlet-api这个jar包，如果在生成war包的时候生效就会和Tomcat中的jar包冲突，导致报错
      -->
        <scope>provided</scope>
      </dependency>
    </dependencies>
  ```

  - 创建一个类实现Servlet接口，并重写接口中的所有方法
  - 配置：在类上使用@WebServlet注解，并配置该Servlet的访问路径

  ```java
  @WebServlet("/demo01")
  public class ServletDemo01 implements Servlet {
      @Override
      public void init(ServletConfig servletConfig) throws ServletException {
          System.out.println("demo01 init");
      }
  
      @Override
      public ServletConfig getServletConfig() {
          return null;
      }
  
      @Override
      public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
          System.out.println("Hello Web!");
      }
  
      @Override
      public String getServletInfo() {
          return null;
      }
  
      @Override
      public void destroy() {
          System.out.println("demo01 destroy");
      }
  }
  ```

  - 访问：启动Tomcat，浏览器输入URL访问该Servlet

  ```
  http://localhost:8080/day12_01_HTTP_Tomcat_Servlet_war_exploded/demo01
  ```

  

- Servlet执行流程
  - 根据`localhost:8080`可以找到要访问的Tomcat Web服务器
  - 根据`day12_01_HTTP_Tomcat_Servlet_war_exploded`可以找到部署在Tomcat服务器上的web-demo项目
  - 根据`demo01`可以找到要访问的是项目中的哪个Servlet类，根据@WebServlet后面的值进行匹配
  - 找到ServletDemo1这个类后，Tomcat Web服务器就会为ServletDemo1这个类创建一个对象，然后调用对象中的service方法



#### Servlet生命周期

![image-20221201211026505](images/image-20221201211026505.png)

- 一个Servlet在Tomcat容器中只会实例化一次，只会产生一个对象，而且常驻内存。要等到服务器关闭才会销毁

- 提前加载Servlet

  - 负整数：第一次被访问时再创建Servlet对象
  - 0或正整数：服务器启动时创建Servlet对象，数字越小优先级越高

  ```java
  @WebServlet(value = "/demo01",loadOnStartup = 1)
  ```



#### HttpServlet

- 对实现了Servlet根接口的Servlet类进行了继承
- 可以根据不同的请求方式，调用不同的doXxx方法

```java
@WebServlet("/demo3")
public class ServletDemo03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("demo3 doGet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("demo3 doPost");
    }
}
```



#### urlPattern配置

- 一个Servlet可以配置多个urlPattern

  ```
  @WebServlet(urlPatterns = {"/demo7","/demo8"})
  ```



- urlPattern配置规则
  - 精确匹配：
    - 配置路径：`@WebServlet("/user/select")`
    - 访问路径：`http://localhost:8080/web-demo/user/select`
  - 目录匹配：
    - 配置路径：`@WebServlet("/user/*")`
    - 访问路径：
      - `http://localhost:8080/web-demo/user/aa`
      - `http://localhost:8080/web-demo/user/a/b`
  - 扩展名匹配：
    - 配置路径：`@WebServlet("*.do")`
    - 访问路径：`http://localhost:8080/web-demo/user/bb.do`
  - 任意匹配：
    - 配置路径：`@WebServlet("/")`、`@WebServlet("/*")`
    - 访问路径：`http://localhost:8080/web-demo/user/haha`
  - 【注意】
    - 如果路径配置的不是扩展名，那么在路径的前面就必须要加`/`否则会报错
    - 如果路径配置的是`*.do`,那么在前面就不能加`/`，否则会报错
    - 优先级为：精确匹配 > 目录匹配> 扩展名匹配 > /* > /



#### XML配置Servlet

- @WebServlet这个是Servlet从3.0版本后开始支持注解配置，3.0版本前只支持XML配置文件的配置方法

- 配置步骤：

  - 编写Servlet类

    ```java
    public class ServletDemo04 extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("demo4 doGet");
        }
    
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            System.out.println("demo04 doPost");
        }
    }
    ```

  - 在`web.xml`中配置该Servlet

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
    	version="4.0">
    
    <!--	XML配置Servlet-->
    <!--	配置servlet的名称-->
    	<servlet>
            <!-- servlet的名称，名字任意-->
    		<servlet-name>xmlDemo</servlet-name>
            <!--servlet的类全名-->
    		<servlet-class>cn.org.none.servlet.ServletDemo04</servlet-class>
    	</servlet>
        
    <!--	配置servlet的路径-->
    	<servlet-mapping>
            <!-- servlet的名称，要和上面的名称一致-->
    		<servlet-name>xmlDemo</servlet-name>
            <!-- servlet的访问路径-->
    		<url-pattern>/demo04</url-pattern>
    	</servlet-mapping>
    
    </web-app>
    ```

    

