#### Filter

- Filter表示过滤器，是JavaWeb是三大组件（Servlet(主要)、Filter(辅助)、Lisetener(辅助)）之一

  ![image-20221102203804502](images/image-20221102203804502.png)

  - 访问被拦截的web资源时首先会经过过滤器，执行放行前的代码
  - 当放行后再访问web资源
  - 最后在返回到过滤器执行放行后的代码



#### Filter拦截路径配置

- 拦截具体的资源：`/index.jsp`只有访问index.jsp时才会被拦截。
- 目录拦截：`/user/*`访问/user下的所有资源，都会被拦截
- 后缀名拦截：`*.jsp`访问后缀名为jsp的资源，都会被拦截
- 拦截所有：`/*`访问所有资源，都会被拦截
- 如果请求的URL地址不存在，但是匹配过滤的地址，还是会执行过滤器



#### 过滤器链

- **注解配置**的Filter，优先级按照过滤器**类名(字符串)的自然排序**

![image-20221208215005481](images/image-20221208215005481.png)



#### XML配置Filter

- **XML配置**的过滤器链按照**书写的先后顺序**进行执行

- 在web.xml中配置

  ```xml
  	<filter>
          <!-- 自定义名称 -->
  		<filter-name>aaa</filter-name>
  		<!-- Filter类路径 -->
          <filter-class>cn.org.none.filter.Demo3Filter</filter-class>
  	</filter>
  	<filter-mapping>
  		<!-- 自定义名称 -->
          <filter-name>aaa</filter-name>
  		<!-- 拦截路径 -->
          <url-pattern>/*</url-pattern>
  	</filter-mapping>
  ```

  

#### Listener

- 三大类八种监听器

  | 监听器分类         | 监听器名称                      | 作用                                               |
  | ------------------ | ------------------------------- | -------------------------------------------------- |
  | ServletContext监听 | ServletContextListener          | 用于对ServletContext对象进行监听（创建、销毁）     |
  |                    | ServletContextAttributeListener | 对ServletContext对象中的属性进行监听（增删改属性） |
  | Session监听        | HttpSessionListener             | 对Session对象的整体状态的监听（创建、销毁）        |
  |                    | HttpSessionAttributeListener    | 对Session对象中的属性监听 (增删改属性)             |
  |                    | HttpSessionBindingListener      | 监听对象于Session的绑定和解除                      |
  |                    | HttpSessionActivationListener   | 对Session数据的钝化和活化的监听                    |
  | Request监听        | ServletRequestListener          | 对Request对象进行监听 (创建、销毁)                 |
  |                    | ServletReguestAttributelistener | 对Request对象中属性的监听(增删改属性)              |



#### ServletContextListener的使用

- 定义实现ServletContextListener接口的类

- 在类上添加@WebListener注解

  ```java
  @WebListener
  public class ContextLoadListener implements ServletContextListener {
      @Override
      public void contextInitialized(ServletContextEvent servletContextEvent) {
          //创建mysql连接池、线程池等操作
          System.out.println("初始化方法");
      }
  
      @Override
      public void contextDestroyed(ServletContextEvent servletContextEvent) {
          //销毁mysql连接池、线程池等操作
          System.out.println("销毁方法");
      }
  }
  ```

  

#### Ajax

- AJAX：Asynchronous JavaScript And XML：异步的JavaScript和XML
- 作用：
  - 异步发送请求
  - 局部刷新页面



#### 原生Ajax使用

- 编写AjaxServlet,使用resopnse输出字符串

- 创建XMLHttpRequest对象：用于和服务器交换数据

  ```js
  let xmlHttpRequest = new XMLHttpRequest();
  ```

- 向服务器发送请求

  ```js
  xmlhttp.open("GET","url");	//设置发送方式和url
  xmlhttp.send();				//发送
  ```

- 获取服务器响应数据

  ```js
  //当ajax状态改变会调用这个函数
  xmlhttp.onreadystatechange = function(){
  	if(xmlhttp.readState == 4 && xmlhttp.status == 200){
  		alert(xmlhttp.response Test);
  	}
  }
  ```



#### Axios框架

- 使用：

  - 引入axios的js文件：`<script src="js/axios-0.18.0.js"></script>`

  - 使用axios发送请求，并获取响应结果

    - GET请求

      ```js
      //resp为自定义名称
      axios({
          method:"get",
          url:"http://localhost:8080/ajax-demo1/aJAXDemo1?username=zhangsan"
      }).then(function (resp){
          alert(resp.data);
      })
      ```

    - POST请求

      ```js
      axios({
          method:"post",
          url:"http://localhost:8080/ajax-demo1/aJAXDemo1",
          data:"username=zhangsan"
      }).then(function (resp){
          alert(resp.data);
      })
      ```

  - 相关参数

    - `method` 属性：用来设置请求方式的。取值为 `get` 或者 `post`
    - `url` 属性：用来书写请求的资源路径。如果是 `get` 请求，需要将请求参数拼接到路径的后面，格式为： `url?参数名=参数值&参数名2=参数值2`
    - `data` 属性：作为请求体被发送的数据。也就是说如果是 `post` 请求的话，数据需要作为 `data` 属性的值
    - `then()` 需要传递一个匿名函数。我们将 `then()` 中传递的匿名函数称为 ==回调函数==，意思是该匿名函数在发送请求时不会被调用，而是在成功响应后调用的函数。而该回调函数中的 `resp` 参数是对响应的数据进行封装的对象，通过 `resp.data` 可以获取到响应的数据

- 使用别名的方式进行使用

  - GET请求

    ```js
    axios.get("url?key1=value1&key2=value2")
        .then(resp=>{
        alert(resp.data);
    	})
    ```

  - POST请求

    ```js
    axios.post("url","key1=value1&key2=value2")
    	.then(resp=>{
    		alert(resp.data);
    	})
    ```

    

