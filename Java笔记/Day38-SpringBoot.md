### SpringBoot

- SpringBoot是**搭建spring应用的脚手架**，基于【约定优于配置】的思想简化配置来进一步简化了Spring应用的整个搭建和开发过程
- 解决的问题：
  - Spring复杂的配置
  - 混乱的依赖管理
- 特点:
  - 快速创建独立的Spring应用
  - 提供固定的启动器依赖简化组件配置，通过自己设置配置文件，即可快速使用
  - 只需要导入框架的启动器，该框架对应的依赖就会全部自动导入
  - 提供了常见的非功能性特性，如内嵌Tomcat服务器（默认端口8080）、安全、指标、健康检测、外部化配置等



### pom.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.itheima</groupId>
    <artifactId>springboot_demo</artifactId>
    <version>1.0-SNAPSHOT</version>


    <!--1. 凡是springboot项目都必须要继承一个父模块，主要是对版本依赖进行统一管理-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
    </parent>


   <!-- 2. 需要使用哪个框架，那么就导入对应框架的启动器即可。
    比如：springmvc - starter-web-->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    
</project>
```



### 启动类

- 启动类就是带`@SpringBootApplication`注解的普通Java类，是运行SpringBoot项目的入口类

- 核心代码

  ```java
  package com.itheima;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  /**
   * 该类就是一个启动类，就是当前程序的入口,启动类要求如下：
   *  1. 类上必须@SpringBootApplication 注解
   *  2. 必须在main里面使用SpringApplication.run(当前类的class，args)，写法是固定的。
   */
  @SpringBootApplication
  public class DemoApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
      }
  }
  ```

- 【Tip】@SpringBootApplication注解的源码有一个@ComponentScan的注解，默认扫描启动类所在包以及它的子包



### 配置文件

- 在父模块中，默认定义了配置文件的命名规范和存放位置
  - 默认存放位置（file: 指当前项目根目录；classpath: 指当前项目的类路径）
    - file:./config/*/
    - file:*/config/
    - file:./
    - classpath:./config/
    - classpath:/
  - 加载顺序（优先级低到高）：yml ---> yaml ---> properties（存在相同的配置内容时，高优先级的内容会覆盖低优先级的内容）

![image-20221226203942202](images/image-20221226203942202.png)

- yml配置文件

  ```yml
  server:
    servlet:
      context-path: /
    port: 8080
  my:
    host: localhost
    port: 1234
    user:
      id: 100
      name: 张三
      age: 23
      # 定义数组时，每个元素必须是”-空格“开头
    address:
      - 北京
      - 上海
      - 深圳
    userList:
      - id: 101
        name: 李四
        age: 24
      - id: 102
        name: 王五
        age: 25
  ```

  

### 读取配置文件

- 方式一：@Value
  - 缺点：只能读取简单类型数据（基本类型+String）
- 方式二：@ConfigurationProperties
  - 属性名必须与配置文件key一致
  - 所有需要注入的属性，都必须提供setter方法
  - 缺点：复用性差

```java
package com.itheima.controller;

import com.itheima.domain.User;
import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Data
@ConfigurationProperties(prefix = "my")
public class PropController {

    //@Value("${my.port}")
    private Integer port;

   // @Value("${my.host}")
    private String host;

    private User user;

    @GetMapping("/prop1")
    public String prop1(){
        System.out.println("读取到的内容：port:"+port+" host:"+host);
        return "success";
    }

    @GetMapping("/prop2")
    public String prop2(){
        System.out.println("读取到的内容：port:"+port+" host:"+host+" user对象："+ user);
        return "success";
    }
}
```



#### 方式三：@ConfigurationProperties + @EnableConfigurationProperties

- `@ConfigurationProperties`: 配置属性
- `@EnableConfigurationProperties(配置类.class)`: 启用配置属性
- 使用时需要注入的属性封装成一个**属性类**，当用到的时候 需要 **启用配置属性**

- 定义属性类

  ```java
  package com.itheima.config;
  
  import com.itheima.domain.User;
  import lombok.Data;
  import org.springframework.boot.context.properties.ConfigurationProperties;
  import org.springframework.stereotype.Component;
  
  import java.util.List;
  
  @Data
  @ConfigurationProperties(prefix = "my")
  //@Component不建议在SpringBoot中使用
  public class PropConfig {
  
      private Integer port;
  
      private String host;
  
      private User user;
  
      private String[] address;
  
      private List<User> userList;
  
  }
  ```

- 启用配置属性

  ```java
  package com.itheima.controller;
  
  import com.itheima.config.PropConfig;
  import com.itheima.domain.User;
  import lombok.Data;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.beans.factory.annotation.Value;
  import org.springframework.boot.context.properties.ConfigurationProperties;
  import org.springframework.boot.context.properties.EnableConfigurationProperties;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  import java.util.Arrays;
  
  @RestController
  @EnableConfigurationProperties(PropConfig.class)
  //在springboot里面创建属性配置类的对象一般别人没有使用@Compnent注解，一般都习惯使用@EnableConfigurationProperties(PropConfig.class)
  public class PropController {
  
      @Autowired
      private PropConfig propConfig;
  
  
      @GetMapping("/prop1")
      public String prop1(){
          System.out.println("读取到的内容：port:"+propConfig.getPort()+" host:"+propConfig.getHost());
          return "success";
      }
  
      @GetMapping("/prop2")
      public String prop2(){
          System.out.println("读取到的内容：port:"+propConfig.getPort()+" host:"+propConfig.getHost()+" user对象："+ propConfig.getUser());
          return "success";
      }
  
  
      @GetMapping("/prop3")
      public String prop3(){
          System.out.println("读取到的内容：port:"+propConfig.getPort()+" host:"+propConfig.getHost()+" user对象："+ propConfig.getUser());
          System.out.println("住房地址："+ Arrays.toString(propConfig.getAddress()));
          System.out.println("用户对象："+ propConfig.getUserList());
          return "success";
      }
  }
  ```



### 整合lombok

- 第一步：在IDEA中安装lombok插件

- 第二步：导入lombok依赖

  ```xml
  <!-- 引入lombok -->
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
  </dependency>
  ```

- 第三步：使用注解

  - `@Data`自动生成getter、setter、hashCode、equals、toString方法
  - `@AllArgsConstructor`自动生成全参构建器
  - `@NoArgsConstructor`自动生成无参构建器
  - `@Slf4j`自动在bean中提供log变量，其实用的是slf4j的日志功能
  - `@Setter`自动生成setter方法
  - `@Getter`自动生成getter方法
  - `@EqualsAndHashCode`自动生成equals、hashCode方法
  - `@ToString`自动生成toString方法
  - `@NonNull`这个注解可以用在**成员方法**或者**构造方法的参数**前面，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常

  ```java
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  @RestController
  @Slf4j //一旦使用@Slf4j这个注解，我们就可以直接使用一个log变量输出日志信息
  public class LogController {
  
      /*
         info warn(警告) ， error(错误级别)
       */
      @GetMapping("/log")
      public String log(){
          
          log.info("======log的普通日志信息=======");
          log.warn("======log的警告日志信息=======");
          log.error("======log的错误日志信息=======");
         //System.out.println("========sout的信息=========");  //sout语句以后不准出现在正常业务代码里面（效率非常低）
          return "log success";
      }
  }
  ```

  

### 整合SpringMVC

- 默认的静态资源访问路径
  - classpath:/META-INF/resources/
  - classpath:/resources/
  - classpath:/static/
  - classpath:/public/



#### 添加拦截器

官方文档翻译如下（<https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-auto-configuration> ）：

> 如果你想要保持Spring Boot 的一些默认MVC特征，同时又想自定义一些MVC配置（包括：拦截器、格式化器、视图控制器、消息转换器 等等），你应该让一个类实现`WebMvcConfigurer`，并且添加`@Configuration`注解，但是**千万不要**加`@EnableWebMvc`注解。如果你想要自定义`HandlerMapping`、`HandlerAdapter`、`ExceptionResolver`等组件，你可以创建一个`WebMvcRegistrationsAdapter`实例 来提供以上组件。
>
> 如果你想要完全自定义SpringMVC，不保留SpringBoot提供的一切特征，你可以自己定义类并且添加`@Configuration`注解和`@EnableWebMvc`注解



**实现步骤**

- 第一步：自定义拦截器实现HandlerInterceptor接口

  ```java
  package com.itheima.interceptor;
  
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.stereotype.Component;
  import org.springframework.web.servlet.HandlerInterceptor;
  import org.springframework.web.servlet.ModelAndView;
  
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  /*
  拦截器的编写步骤：
      1. 自定义一个类实现HandlerInterceptor
   */
  @Slf4j
  @Component
  public class Demo1Interceptor implements HandlerInterceptor {
  
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          log.info("【前置通知】");
          return true;
      }
  
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          log.info("【后置通知】");
      }
  
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          log.info("【最终通知】");
      }
  }
  ```

  

- 第二步：自定义配置类实现WebMvcConfigurer接口，注册拦截器

  ```java
  package com.itheima.config;
  
  import com.itheima.interceptor.Demo1Interceptor;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
  
  @Configuration
  public class SpringMvcConfig implements WebMvcConfigurer {
  
      @Autowired
      private Demo1Interceptor demo1Interceptor;
  
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(demo1Interceptor).addPathPatterns("/log");
      }
  }
  ```

  

### 整合Mybatis

- 第一步：导入Mybatis启动器依赖(**它依赖了jdbc启动器，jdbc启动器可以删除**)

  ```xml
  <!--myabtis-->
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.0</version>
  </dependency>
  ```

- 第二步：在application.yml中进行相关配置

  ```properties
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql:///springdb?useSSL=false
      username: root
      password: root
  mybatis:
    #别名扫描，别名扫描实体类的包作用就是可以直接类名在resultType里面
    type-aliases-package: com.itheima.domain
    configuration:
      #开启下划线与小驼峰映射
      map-underscore-to-camel-case: true
      #开启sql日志记录
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    #指定mapper文件所在的目录，一旦指定之后，那么xml文件可以与接口不在同一个包下了，如果不指定是需要在同一个目录下
    mapper-locations:
      - classpath:mapper/*.xml #这里代表的是一个数组的元素，是一个整体，不需要有空格
  ```

  

- 数据访问接口注解

  - 方式一：在接口类上使用@Mapper注解

    ```java
    @Mapper
    public interface UserDao {
    
        public List<User> findAll();
    }
    ```

  - 方式二：在启动类上添加数据访问接口包扫描

    ```java
    package com.itheima;
    
    import org.mybatis.spring.annotation.MapperScan;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    /**
     * 注意： 使用mybatis的时候，这个springboot默认是不扫描dao接口的，两种解决方案：
     *      1. 每一个dao接口都添加一个@mapper注解,不推荐，因为较为繁琐
     *      2. 在启动类中@MapperScan注解扫描dao包
     */
    @SpringBootApplication
    @MapperScan(basePackages = "com.itheima.dao")
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

    

### 整合Junit

- 导入test启动器依赖

  ```xml
  <!-- 配置test启动器(自动整合spring-test、junit) -->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
  </dependency>
  ```

- 编写测试类

  ```java
  package com.itheima.test;
  
  import com.itheima.MybatisApplication;
  import com.itheima.entity.User;
  import com.itheima.service.UserService;
  import org.junit.jupiter.api.Test;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.boot.test.context.SpringBootTest;
  
  import java.util.List;
  /*
  注意的事项：
      1. 测试类如果与启动类在同一个包或者是子包，那么测试类可以不指定启动类的位置。
      2. 测试类不在启动类在同一个包或者是子包，测试类必须指明启动类的位置
   */
  //@SpringBootTest(classes = MybatisApplication.class)   //不在子包下则需要指定启动类的位置
  @SpringBootTest
  public class AppTest {
  
      @Autowired
      private UserService userService;
  
      @Test
      public void testFindAll(){
          List<User> userList = userService.findAll();
          System.out.println("用户列表："+userList);
      }
  }
  ```

  

### 项目打包部署

- 第一步：导入打包插件

  ```xml
      <!--打包的插件-->
      <build>
          <!--修改jar的名字-->
          <!--可省略 <finalName>ROOT</finalName> -->
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
  ```

- 第二步：执行打包命令（需要配置maven系统变量，然后在项目路径下使用该命令）

  ```cmd
  # 清理、打包 跳过测试
  mvn clean package -Dmaven.test.skip=true
  ```

- 第三步：运行打包好的jar包

  ```
  java -jar xxx.jar
  ```

  

### 多环境动态切换（profile配置）

> 我们在开发Spring Boot应用时，通常同一套程序会被安装到不同环境，比如：开发、测试、生产等。其中数据库地址、服务器端口等等配置都不同，如果每次打包时，都要修改配置文件，那么非常麻烦。profile功能就是来进行动态配置切换的。 



**多文件方式**

- profile文件设置（提供多个配置文件，每种文件代表一种环境）
  - application-dev.properties/yml：开发环境
  - application-test.properties/yml：测试环境
  - application-pro.properties/yml：生产环境
- 激活方式：在application.properties配置文件中配置：`spring.profiles.active=dev`

**yml单文件方式**

- 在application.yml配置文件中写上三种环境的配置信息并激活

  ```yml
  ---
  server:
    port: 8082
  spring:
    profiles: dev
  ---
  server:
    port: 8083
  spring:
    profiles: test
  ---
  server:
    port: 8084
  spring:
    profiles: pro
  ---
  # 激活配置
  spring:
    profiles:
      active: test
  ```

  

- 其他激活方式

  - VM Options 参数：`-Dspring.profiles.active=dev`

    ![image-20210128174609855](images/image-20210128174609855.png)

  - Program  arguments参数：`--spring.profiles.active=dev`

    ![image-20210128174846696](images/image-20210128174846696.png)

  - 命令行参数：`java –jar xxx.jar --spring.profiles.active=dev`



