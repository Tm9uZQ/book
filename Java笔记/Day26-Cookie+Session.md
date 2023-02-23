#### 会话跟踪技术

- 实现在同一会话内多次请求间的数据共享



#### Cookie

- **基于HTTP协议**的一种客户端会话跟踪技术，将数据保存到客户端，以后每次请求都携带Cookie数据进行访问，用于实现一次会话的多次请求间数据共享功能
- 由服务器创建，发送给浏览器
- 发送cookie
  - 创建cookie：`Cookie cookie = new Cookie("key","value");`
  - 发送Cookie到客户端：`response.addCookie(cookie);`
- 获取cookie
  - 获取客户端发送的所有cookie：`Cookie[] cookies = request.getCookies();` 
  - 获取cookie对象的数据：`cookie.getName();/cookie.getValue();`



#### Cookie使用细节

- **Cookie存活时间**：默认情况下cookie存储在浏览器内存中，当浏览器关闭，内存释放，则cookie被销毁

- `cookie.setMaxAge(int seconds)`：设置cookie存活时间（秒）【一般int秒，long毫秒】

  - 正数：将 Cookie写入浏览器所在电脑的硬盘，持久化存储。到时间自动删除
  - 负数：默认值，Cookie在当前浏览器内存中，当浏览器关闭，则 Cookie被销毁
  - 零：立即过期

- **cookie存储问题**：

  - Tomcat7的Cookie值不能直接存储中文，Tomcat8的Cookie值可以存储中文，但不能存储空格
  - 如需要存储空格，则需要进行转码：URL编码
  
  ```java
  //发送cookie
  @WebServlet(value = "/a")
  public class AServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //存储空格的问题，需要进行编码
          String value = "张 三";
          //进行URL编码
          value = URLEncoder.encode(value,"utf-8");
          //创建cookie
          Cookie cookie = new Cookie("call", value);
          //cookie.setMaxAge()设置存活时间
          cookie.setMaxAge(60);//存活60秒，
          //发送cookie
        response.addCookie(cookie);
      }
  ```
  
  ```java
  //接收cookie
  @WebServlet(value = "/b")
  public class BServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //获取所有cookie对象
          Cookie[] cookies = request.getCookies();
          //遍历cookie
          if (cookies != null) {
              //获取每一个cookie
              for (Cookie cookie : cookies) {
                  String name = cookie.getName();
                  String value = cookie.getValue();
                  //URL解码
                  value = URLDecoder.decode(value,"utf-8");
                  System.out.println(name + ":" + value);
              }
          }else {
              System.out.println("cookie不存在");
        }
      }
  ```
  
  

#### Session

- **基于Cookie**实现的服务端会话跟踪技术，将数据保存到服务端，来实现一次会话的多次请求间数据共享功能
- Session保存在服务器中，SessionID发送给浏览器
- 获取Session对象：`HttpSession session = request.getSession();`
- 使用session
  - 存储数据到session域中：`void setAttribute(String name, Object o);`
  - 根据key获取值：`Object getAttribute(String name);`



#### Session使用细节

- 钝化、活化

  - 钝化：在服务器正常关闭后， Tomcat自动将 Session数据写入硬盘的文件中（IDEA需要进行配置）
  - 活化：再次启动服务器后，从文件中加载数据到Session中（原文件删除）

- 销毁

  - 手动调用session对象的invalidate()方法销毁

  - 默认情况下，无操作，30分钟自动销毁

  - 可通过web.xml进行配置（单位：分钟）

    ```xml
    <session-config>
    		<session-timeout>5</session-timeout>
    </session-config>
    ```

- 代码

  ```java
  @WebServlet(value = "/c")
  public class CServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //get请求
          //获取session对象
          HttpSession session = request.getSession();
          //获取sessionID，并判断session是否为新
          System.out.println("sessionID：" + session.getId() + "是否为新建：" + session.isNew());
          //查看session的存活时间，可通过web.xml进行设置
          System.out.println("MaxInactiveInterval = " + session.getMaxInactiveInterval());
          //存储数据到session对象
          session.setAttribute("sname", "lisi");
          //获取session中的数据
          System.out.println("sname" + session.getAttribute("sname"));
  
      }
  ```

  

#### Cookie和Session的对比

- **相同点**：
  - Cookie 和 Session 都是来完成一次会话内多次请求间数据共享的
- **区别**
  - 键值对数量：一个Cookie 存一个键和一个值，一个Session 可以存n个键和值
  - 存储位置：Cookie 是将数据存储在客户端，Session 将数据存储在服务端
  - 安全性：Cookie 不安全，Session 安全
  - 数据大小：Cookie 最大4KB，Session 无大小限制
  - 存储时间：Cookie默认浏览器关闭，Session 默认30分钟
  - 服务器性能：Cookie 不占服务器资源，Session 占用服务器资源



#### 应用场景

- Cookie是用来保证用户在未登录情况下的身份识别
- Session是用来保存用户登录后的数据保存