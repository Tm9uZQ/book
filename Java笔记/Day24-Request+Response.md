### Requset

- request：后台服务器（Tomcat）会对HTTP请求中的数据进行解析，并把解析结果存入到request对象

  

#### Requset获取请求数据

- 请求行：`GET /request-demo/req1?username=zhangsan HTTP/1.1 `

  - `String getMethod()`获取请求方式：`GET`
  - `String getContextPath()`获取虚拟目录（项目访问路径）：`/request-demo`
  - `String getRequestURI()`获取URI（统一资源标识符）：`/request-demo/req1`
  - `StringBuffer getRequestURL()`获取URL（同一资源定位符）：`http://localhost:8080/request-demo/req1`
  - `String getQueryString()`获取请求参数（get方式）：`username=zhangsan HTTP/1.1`

- 请求头：

  - `String getHeader(String name)`

  ```java
  //获取浏览器的版本信息
  String agent = request.getHeader("user-agent");
  ```

- 请求体（post方式）：

  - 字节文件用：`request.getInputStream()`
  - 纯文本用：`request.getReader()`

```java
@WebServlet(value = "/Demo1")
public class Demo1Request extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //get请求
        System.out.println("请求方式 = " + request.getMethod());
        System.out.println("项目访问路径 = " + request.getContextPath());
        System.out.println("URI = " + request.getRequestURI());
        System.out.println("URL = " + request.getRequestURL());
        System.out.println("请求参数 = " + request.getQueryString());
        System.out.println("浏览器版本 = " + request.getHeader("user-agent"));
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //post请求
        //doGet(request, response);
        BufferedReader reader = request.getReader();
        String s = reader.readLine();
        System.out.println("POST请求参数 = " + s);
    }
}

//ufferedReader流是通过request对象来获取的，当请求完成后request对象就会被销毁，request对象被销毁后，BufferedReader流就会自动关闭，所以此处就不需要手动关闭流了
```



#### 通用方式获取请求参数

- GET 请求方式 和 POST 请求方式 区别主要在于获取请求参数的方式不一样，request对象已经将获取请求参数的方法进行了封装，将请求参数进行了分割，存入到`Map<String,String[]>`集合中

```java
@WebServlet(value = "/Demo2")
public class Demo2Request extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //get请求
        //Tomcat8.0之后，已将GET请求乱码问题解决，设置默认的解码方式为UTF-8
        //POST的请求参数是通过request的getReader()来获取流中的数据，tomcat在获取流的时候采用的编码是ISO-8859-1，而该字符集是不支持中文的
        //需要解决POST请求中文乱码问题
        String method = request.getMethod();
        if (method.equalsIgnoreCase("post")) {
            request.setCharacterEncoding("UTF-8");
        }

        //获取单个参数对应的值
        String name = request.getParameter("username");
        System.out.println("username = " + name);
        //获取单个参数对应的数组
        String[] hobbies = request.getParameterValues("hobby");
        System.out.println("hobbies = " + Arrays.toString(hobbies));
        
        //获取所有参数的Map集合
        Map<String, String[]> map = request.getParameterMap();
        map.forEach((key, values) -> {
            System.out.println(key + ":" + Arrays.toString(values));
        });

    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //post请求
        doGet(request, response);
    }
}
```



#### 请求转发

- 请求转发（forward）：一种在==服务器内部==的资源跳转方式

  - 浏览器只发出**一次**请求，URL**不改变**
  - 只能转发到当前服务器**内部**的资源
  - 可以在转发的资源间**共享数据**

  ![1628851404283](images/1628851404283.png)

```java
//实现方式
request.getRequestDispatcher("资源B路径").forward(request,response);
```

```java
//A资源
@WebServlet(value = "/a")
public class A_Servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //设置资源共享（要在转发前面）
        request.setAttribute("name","zhangsan");
        //请求转发
        request.getRequestDispatcher("b").forward(request,response);
    }
```

```java
//B资源
@WebServlet(value = "/b")
public class B_Servlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //接受共享的资源
        Object name = request.getAttribute("name");
        System.out.println("name = " + name);
    }
```



#### Response

- 常用方法

  ```java
  @WebServlet(value = "/Demo3")
  public class Demo3Response extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
  
          /**Response获取的字符输出流默认编码ISO-8859-1
           * 解决中文乱码问题：
           * 1.设置响应类型为：text/html
           * 2.设置编码格式：charset=utf-8
           */
          response.setContentType("text/html;charset=utf-8");
          
          //设置响应行
          response.setStatus(404);
          //设置响应头
          response.setHeader("content-type","text/html");
          
          //获取响应字符输出流
          PrintWriter writer = response.getWriter();
          //写数据
          writer.write("<h1 align='center'>你好 Demo4</h1>");
      }
      //该流不需要关闭，随着响应结束，response对象销毁，由服务器关闭
  ```

- IOUtils工具类

  - 导入坐标

    ```xml
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.11.0</version>
    </dependency>
    ```

  - 使用IOUtils工具类进行输出

    ```java
    @WebServlet(value = "/Demo5")
    public class Demo5Servlet extends HttpServlet {
        @Override
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            //get请求
            FileInputStream fis = new FileInputStream("F:\\java_code\\JavaSE182\\day12.03_Request_Response\\src\\main\\webapp\\imgs\\Desert.jpg");
            ServletOutputStream os = response.getOutputStream();
    
    //        byte[] bytes = new byte[1024 * 8];
    //        int len;
    //        while ((len = fis.read(bytes)) != -1) {
    //            os.write(bytes, 0, len);
    //        }
            
            IOUtils.copy(fis,os);
    
            fis.close();
        }
    ```

    



#### Response重定向

- 重定向（redirect）：一种资源跳转方式

  - 浏览器发出**两次**请求，URL发生**改变**
  - 可以重定向到**任意位置**的资源（服务器内部、外部均可）
  - **不能**在资源间共享数据

  ![1628859860279](images/1628859860279.png)

```java
//实现方式1
response.setStatus(302);
response.setHeader("location","资源B的路径");
//实现方式2
response.sendRedirect("资源B的路径");
```



#### 路径问题

- 一般来说，在服务器内部进行跳转不需要添加"/"，一般只有使用@WebServlet("/demo")一定要添加"/"
- 从浏览器跳转需要添加"/"



#### 登录&注册案例

- 登录
  - 用户填写用户名密码，提交到 LoginServlet
  - 在 LoginServlet中使用 MyBatis查询数据库，验证用户名密码是否正确
  - 如果正确，响应“登录成功”，如果错误，响应“登录失败”
- 注册
  - 用户填写用户名、密码等信息，点击注册按钮，提交到 RegisterServlet
  - 在 RegisterServlet 中使用 MyBatis 保存数据
  - 保存前，需要判断用户名是否已经存在：根据用户名查询数据库

```java
@WebServlet(value = "/loginServlet")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //get请求
        //解决中文报错
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //获取请求参数
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        //调用Mapper查询用户信息
        SqlSession sqlSession = MyBatisUtils.openSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.select(username, password);
        //输出登录信息
        PrintWriter writer = response.getWriter();
        if (user == null) {
            writer.write("登录失败，用户名或密码错误！");
        } else {
            writer.write("欢迎" + username + "登录成功！");
        }
        //释放资源
        sqlSession.close();
    }
```

```java
@WebServlet(value = "/registerServlet")
public class RegisterServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //get请求
        //解决中文报错
        request.setCharacterEncoding("utf-8");
        response.setContentType("text/html;charset=utf-8");
        //获取请求参数
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        //调用mapper进行判断
        SqlSession sqlSession = MyBatisUtils.openSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.selectByName(username);
        PrintWriter writer = response.getWriter();
        if (user == null) {
            mapper.addUser(username,password);
            //【提交事务】
            sqlSession.commit();
            writer.write("恭喜"+username+"注册成功！");
        }else {
            writer.write("该用户名已经存在，请重新注册");
        }
        //释放资源
        sqlSession.close();
    }
```

```java
public interface UserMapper {
    @Select("select * from tb_user where username=#{uname} and password=#{psw}")
    User select(@Param("uname") String username, @Param("psw") String password);

    @Select("select * from tb_user where username=#{username}")
    User selectByName(String username);

    @Insert("insert into tb_user values (null,#{username},#{password})")
    void addUser(@Param("username")String username, @Param("password")String password);
}
```

