## 黑马商城

### 主体结构

![image-20210412141711919](images/image-20210412141711919.png)



### JWT

> 基于token的用户认证是一种**服务端无状态**的认证方式，所谓服务端无状态指的token本身包含登录用户所有的相关数据，而客户端在认证后的每次请求都会携带token，因此服务器端无需存放token数据。
>
> 当用户认证后，服务端生成一个token发给客户端，客户端可以放到 cookie 或 localStorage 等存储中，每次请求时带上 token，服务端收到token通过验证后即可确认用户身份。



- JSON Web Token（JWT）是一个开放的行业标准（RFC 7519），它定义了一种简洁的、自包含的协议格式，用于在通信双方传递json对象，传递的信息经过数字签名可以被验证和信任。

- JWT结构：JWT令牌由Header、Payload、Signature三部分组成，每部分中间使用点（.）分隔
  - Header：存放令牌的类型（即JWT）及使用的哈希算法（如HMAC、SHA256或RSA）
  - Payload：存放有效信息的地方，有iss（签发者），exp（过期时间戳）， sub（面向的用户）等
  - Signature：签名，此部分用于防止jwt内容被篡改



### 生成jwt代码实现

- 导入依赖

  ```xml
  <dependency>
      <groupId>io.jsonwebtoken</groupId>
      <artifactId>jjwt</artifactId>
  </dependency>
  ```

- 工具类

  ```java
  package com.heima.utils.common;
  
  import io.jsonwebtoken.*;
  
  import javax.crypto.SecretKey;
  import javax.crypto.spec.SecretKeySpec;
  import java.util.*;
  
  public class AppJwtUtil {
  
      // TOKEN的有效期一天（S）
      private static final int TOKEN_TIME_OUT = 3_600;
      // 加密KEY
      private static final String TOKEN_ENCRY_KEY = "MDk4ZjZiY2Q0NjIxZDM3M2NhZGU0ZTgzMjYyN2I0ZjY";
      // 最小刷新间隔(S)
      private static final int REFRESH_TIME = 300;
  
      // 生产ID
      public static String getToken(Long id){
          Map<String, Object> claimMaps = new HashMap<>();
          claimMaps.put("id",id);
          long currentTime = System.currentTimeMillis();
          return Jwts.builder()
                  .setId(UUID.randomUUID().toString())
                  .setIssuedAt(new Date(currentTime))  //签发时间
                  .setSubject("system")  //说明
                  .setIssuer("heima") //签发者信息
                  .setAudience("app")  //接收用户
                  .compressWith(CompressionCodecs.GZIP)  //数据压缩方式
                  .signWith(SignatureAlgorithm.HS512, generalKey()) //加密方式
                  .setExpiration(new Date(currentTime + TOKEN_TIME_OUT * 1000))  //过期时间戳
                  .addClaims(claimMaps) //cla信息
                  .compact();
      }
  
      /**
       * 获取token中的claims信息
       *
       * @param token
       * @return
       */
      private static Jws<Claims> getJws(String token) {
              return Jwts.parser()
                      .setSigningKey(generalKey())
                      .parseClaimsJws(token);
      }
  
      /**
       * 获取payload body信息
       *
       * @param token
       * @return
       */
      public static Claims getClaimsBody(String token) {
          try {
              return getJws(token).getBody();
          }catch (ExpiredJwtException e){
              return null;
          }
      }
  
      /**
       * 获取hearder body信息
       *
       * @param token
       * @return
       */
      public static JwsHeader getHeaderBody(String token) {
          return getJws(token).getHeader();
      }
  
      /**
       * 是否过期
       *
       * @param claims
       * @return -1：有效，0：有效，1：过期，2：过期
       */
      public static int verifyToken(Claims claims) {
          if(claims==null){
              return 1;
          }
          try {
              claims.getExpiration()
                      .before(new Date());
              // 需要自动刷新TOKEN
              if((claims.getExpiration().getTime()-System.currentTimeMillis())>REFRESH_TIME*1000){
                  return -1;
              }else {
                  return 0;
              }
          } catch (ExpiredJwtException ex) {
              return 1;
          }catch (Exception e){
              return 2;
          }
      }
  
      /**
       * 由字符串生成加密key
       *
       * @return
       */
      public static SecretKey generalKey() {
          byte[] encodedKey = Base64.getEncoder().encode(TOKEN_ENCRY_KEY.getBytes());
          SecretKey key = new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
          return key;
      }
  
      public static void main(String[] args) {
          //1.获取token
          String token = AppJwtUtil.getToken(1l);
          System.out.println(token);
  
          //2.校验token是否有效
          Claims claimsBody = AppJwtUtil.getClaimsBody(token);
          System.out.println(claimsBody);
          int i = AppJwtUtil.verifyToken(claimsBody);
          System.out.println(i);
          if(i==-1 || i==0){
              System.out.println("token还处于有效期");
          }else{
              System.out.println("token已经无效");
          }
  
  
      }
  }
  ```



#### 登录功能实现

- 实体类

  ```java
  package com.heima.model.user.pojos;
  
  import com.baomidou.mybatisplus.annotation.IdType;
  import com.baomidou.mybatisplus.annotation.TableField;
  import com.baomidou.mybatisplus.annotation.TableId;
  import com.baomidou.mybatisplus.annotation.TableName;
  import lombok.Data;
  
  import java.io.Serializable;
  import java.util.Date;
  
  /**
   * <p>
   * APP用户信息表
   * </p>
   *
   * @author itheima
   */
  @Data
  @TableName("ap_user")
  public class ApUser implements Serializable {
  
      private static final long serialVersionUID = 1L;
  
      /**
       * 主键
       */
      @TableId(value = "id", type = IdType.AUTO)
      private Integer id;
  
      /**
       * 密码、通信等加密盐
       */
      @TableField("salt")
      private String salt;
  
      /**
       * 用户名
       */
      @TableField("name")
      private String name;
  
      /**
       * 密码,md5加密
       */
      @TableField("password")
      private String password;
  
      /**
       * 手机号
       */
      @TableField("phone")
      private String phone;
  
      /**
       * 头像
       */
      @TableField("image")
      private String image;
  
      /**
       * 0 男
              1 女
              2 未知
       */
      @TableField("sex")
      private Boolean sex;
  
      /**
       * 0 未
              1 是
       */
      @TableField("is_certification")
      private Boolean certification;
  
      /**
       * 是否身份认证
       */
      @TableField("is_identity_authentication")
      private Boolean identityAuthentication;
  
      /**
       * 0正常
              1锁定
       */
      @TableField("status")
      private Boolean status;
  
      /**
       * 0 普通用户
              1 自媒体人
              2 大V
       */
      @TableField("flag")
      private Short flag;
  
      /**
       * 注册时间
       */
      @TableField("created_time")
      private Date createdTime;
  
  }
  ```



- LoginDto

  ```java
  @Data
  public class LoginDto {
      /**
       * 手机号
       */
      private String phone;
      /**
       * 密码
       */
      private String password;
  }
  ```



- 持久层mapper

  ```java
  package com.heima.user.mapper;
  
  import com.baomidou.mybatisplus.core.mapper.BaseMapper;
  import com.heima.model.user.pojos.ApUser;
  import org.apache.ibatis.annotations.Mapper;
  
  @Mapper
  public interface ApUserMapper extends BaseMapper<ApUser> {
  }
  ```



- 业务层service

  ```java
  package com.heima.user.service;
  
  import com.baomidou.mybatisplus.extension.service.IService;
  import com.heima.model.common.dtos.ResponseResult;
  import com.heima.model.user.dtos.LoginDto;
  import com.heima.model.user.pojos.ApUser;
  
  public interface ApUserService extends IService<ApUser>{
  
      /**
       * app端登录
       * @param dto
       * @return
       */
      public ResponseResult login(LoginDto dto);
      
  }
  ```



- 实现类

  ```java
  package com.heima.user.service.impl;
  
  import com.baomidou.mybatisplus.core.toolkit.Wrappers;
  import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
  import com.heima.model.common.dtos.ResponseResult;
  import com.heima.model.common.enums.AppHttpCodeEnum;
  import com.heima.model.user.dtos.LoginDto;
  import com.heima.model.user.pojos.ApUser;
  import com.heima.user.mapper.ApUserMapper;
  import com.heima.user.service.ApUserService;
  import com.heima.utils.common.AppJwtUtil;
  import org.apache.commons.lang3.StringUtils;
  import org.springframework.stereotype.Service;
  import org.springframework.util.DigestUtils;
  
  import java.util.HashMap;
  import java.util.Map;
  
  
  @Service
  public class ApUserServiceImpl extends ServiceImpl<ApUserMapper, ApUser> implements ApUserService {
  
      @Override
      public ResponseResult login(LoginDto dto) {
  
          //1.正常登录（手机号+密码登录）
          if (!StringUtils.isBlank(dto.getPhone()) && !StringUtils.isBlank(dto.getPassword())) {
              //1.1查询用户
              ApUser apUser = getOne(Wrappers.<ApUser>lambdaQuery().eq(ApUser::getPhone, dto.getPhone()));
              if (apUser == null) {
                  return ResponseResult.errorResult(AppHttpCodeEnum.DATA_NOT_EXIST,"用户不存在");
              }
  
              //1.2 比对密码
              String salt = apUser.getSalt();
              String pswd = dto.getPassword();
              pswd = DigestUtils.md5DigestAsHex((pswd + salt).getBytes());
              if (!pswd.equals(apUser.getPassword())) {
                  return ResponseResult.errorResult(AppHttpCodeEnum.LOGIN_PASSWORD_ERROR);
              }
              //1.3 返回数据  jwt
              Map<String, Object> map = new HashMap<>();
              map.put("token", AppJwtUtil.getToken(apUser.getId().longValue()));
              apUser.setSalt("");
              apUser.setPassword("");
              map.put("user", apUser);
              return ResponseResult.okResult(map);
          } else {
              //2.游客  同样返回token  id = 0
              Map<String, Object> map = new HashMap<>();
              map.put("token", AppJwtUtil.getToken(0l));
              return ResponseResult.okResult(map);
          }
      }
  }
  ```



- 控制层controller

  ```java
  package com.heima.user.controller.v1;
  
  import com.heima.model.common.dtos.ResponseResult;
  import com.heima.model.user.dtos.LoginDto;
  import com.heima.user.service.ApUserService;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.web.bind.annotation.PostMapping;
  import org.springframework.web.bind.annotation.RequestBody;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  @RestController
  @RequestMapping("/api/v1/login")
  public class ApUserLoginController {
  
      @Autowired
      private ApUserService apUserService;
  
      @PostMapping("/login_auth")
      public ResponseResult login(@RequestBody LoginDto dto) {
          return apUserService.login(dto);
      }
  }
  ```



#### 网关实现jwt校验

![image-20210705110434492](images/image-20210705110434492.png)

- 网关微服务中新建全局过滤器

  ```java
  package com.heima.app.gateway.filter;
  
  
  import com.heima.app.gateway.util.AppJwtUtil;
  import io.jsonwebtoken.Claims;
  import lombok.extern.slf4j.Slf4j;
  import org.apache.commons.lang.StringUtils;
  import org.springframework.cloud.gateway.filter.GatewayFilterChain;
  import org.springframework.cloud.gateway.filter.GlobalFilter;
  import org.springframework.core.Ordered;
  import org.springframework.http.HttpStatus;
  import org.springframework.http.server.reactive.ServerHttpRequest;
  import org.springframework.http.server.reactive.ServerHttpResponse;
  import org.springframework.stereotype.Component;
  import org.springframework.web.server.ServerWebExchange;
  import reactor.core.publisher.Mono;
  
  @Component
  @Slf4j
  public class AuthorizeFilter implements Ordered, GlobalFilter {
      @Override
      public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
          //1.获取request和response对象
          ServerHttpRequest request = exchange.getRequest();
          ServerHttpResponse response = exchange.getResponse();
  
          //2.判断是否是登录
          if(request.getURI().getPath().contains("/login/login_auth")){
              //放行
              return chain.filter(exchange);
          }
  
  
          //3.获取token
          String token = request.getHeaders().getFirst("token");
  
          //4.判断token是否存在
          if(StringUtils.isBlank(token)){
              response.setStatusCode(HttpStatus.UNAUTHORIZED);
              return response.setComplete();
          }
  
          //5.判断token是否有效
          try {
              Claims claimsBody = AppJwtUtil.getClaimsBody(token);
              //是否是过期
              int result = AppJwtUtil.verifyToken(claimsBody);
              if(result == 1 || result  == 2){
                  response.setStatusCode(HttpStatus.UNAUTHORIZED);
                  return response.setComplete();
              }
          }catch (Exception e){
              e.printStackTrace();
              response.setStatusCode(HttpStatus.UNAUTHORIZED);
              return response.setComplete();
          }
  
          //6.放行
          return chain.filter(exchange);
      }
  
      /**
       * 优先级设置  值越小  优先级越高
       * @return
       */
      @Override
      public int getOrder() {
          return 0;
      }
  }
  ```



### 日志文件

- 在resources文件夹下创建logback.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  
  <configuration>
      <!--定义日志文件的存储地址,使用绝对路径-->
      <property name="LOG_HOME" value="e:/logs"/>
  
      <!-- Console 输出设置 -->
      <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
          <encoder>
              <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
              <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
              <charset>utf8</charset>
          </encoder>
      </appender>
  
      <!-- 按照每天生成日志文件 -->
      <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
          <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
              <!--日志文件输出的文件名-->
              <fileNamePattern>${LOG_HOME}/leadnews.%d{yyyy-MM-dd}.log</fileNamePattern>
          </rollingPolicy>
          <encoder>
              <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
          </encoder>
      </appender>
  
      <!-- 异步输出 -->
      <appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
          <!-- 不丢失日志.默认的,如果队列的80%已满,则会丢弃TRACT、DEBUG、INFO级别的日志 -->
          <discardingThreshold>0</discardingThreshold>
          <!-- 更改默认的队列的深度,该值会影响性能.默认值为256 -->
          <queueSize>512</queueSize>
          <!-- 添加附加的appender,最多只能添加一个 -->
          <appender-ref ref="FILE"/>
      </appender>
  
  
      <logger name="org.apache.ibatis.cache.decorators.LoggingCache" level="DEBUG" additivity="false">
          <appender-ref ref="CONSOLE"/>
      </logger>
      <logger name="org.springframework.boot" level="debug"/>
      <root level="info">
          <!--<appender-ref ref="ASYNC"/>-->
          <appender-ref ref="FILE"/>
          <appender-ref ref="CONSOLE"/>
      </root>
  </configuration>
  ```

  