### Redis

> Redis是用C语言开发的一个开源的内存中的数据结构存储系统，它可以用作：数据库、缓存和消息中间件

- Redis是一款==非关系型数据库==，redis存储的数据是在==内存==中
  - 关系型数据库（RDBMS）：MySQL、Oracle、DB2、SQLServer
  - 非关系型数据库（Nosql）：Redis、MongoDB、MemCached
- Redis是一个基于**内存**的key-value结构数据库
  - 基于内存存储，读写性能高
  - 适合存储热点数据（热点商品、咨询、新闻）
- 能做什么
  - 数据缓存
  - 消息队列
  - 注册中心
  - 发布订阅



### 部署

- Windows版下载地址：https://github.com/microsoftarchive/redis/releases
- Linux版下载地址： https://download.redis.io/releases/ 

- Redis默认端口号为==6379==

在Linux系统安装Redis步骤：

1. 将Redis安装包上传到Linux到soft目录
2. 解压安装包，命令：==tar -xvf redis-4.0.0.tar.gz -C /usr/local==
3. 安装Redis的依赖环境gcc，命令：==yum install gcc-c++==
4. 进入 ==cd  /usr/local/redis-4.0.0，进行编译，命令： make==
5. 进入redis的src目录进行安装，命令：==make install==
6. 进入/usr/local/redis-4.0.0  ,把redis.conf文件拷贝到src目录中  ==cp  /usr/local/redis-4.0.0/redis.conf   /usr/local/redis-4.0.0/src/==
7. 修改redis.conf文件，需要修改的地方有：
   1.  修改redis.conf文件，让其在后台启动不要霸屏的方式启动，	将配置文件中的==daemonize==配置项改为yes，默认值为no
   2.  reids默认是没有密码的，如果你需要有密码，将配置文件中的 ==# requirepass foobared== 配置项取消注释，默认为注释状态，foobared为密码，可以根据情况自己指定
   3.  redis的服务默认只是允许本机连接，其他机器默认情况是不被允许连接，如果允许其他机器也能连接linux的reids服务，那么需要修改==bind 127.0.0.1  你自己的linux机器的ip地址==
8. 启动redis的服务， 使用  redis-server redis.conf
9. 开放6379的端口号： ==firewall-cmd --zone=public --add-port=6379/tcp --permanent==
10. 重新加载防火墙 ： ==firewall-cmd --reload==



### 数据类型

- 字符串String
- 哈希Hash
- 列表List（可重复）
- 集合Set（不可重复）
- 有序集合Sorted set / zset

![1669686417289](images/1669686417289.png)



### 常用命令

- 参考Redis中文网：https://www.redis.net.cn/order/



### Java操作Redis

- 导入依赖

  ```xml
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>2.8.0</version>
  </dependency>
  ```

- 步骤

  - 获取连接
  - 执行操作
  - 关闭连接

  ```java
  @Test
  public void testJedis(){
      //1. 创建Jedis，并且连接redis的服务端
      Jedis jedis = new Jedis("192.168.65.10",6379);
      //2. 添加数据
      jedis.set("name","张三");
      System.out.println("数据："+ jedis.get("name"));
      //3. 关闭资源
      jedis.close();
  }
  ```



#### Spring Data Redis

- 导入依赖

  - Mevan

  ```xml
  <dependency>
  	<groupId>org.springframework.data</groupId>
  	<artifactId>spring-data-redis</artifactId>
  	<version>2.4.8</version>
  </dependency>
  ```

  - SpringBoot

  ```xml
  <dependency>
  	<groupId>org.springframework.boot</groupId>
  	<artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ```

- RedisTemplate的operation接口

  - ValueOperations：简单K-V操作
  - SetOperations：set类型数据操作
  - ZSetOperations：zset类型数据操作
  - HashOperations：针对hash类型的数据操作
  - ListOperations：针对list类型的数据操作

- application.yml的Redis配置

  ```yml
  spring:
    redis:
      host: 192.168.65.10
      port: 6379
      password: root #如果有
      database: 0 #redis有16个数据库，默认是0
      jedis:
        pool:
          max-active: 10 #最大链接数
          max-idle: 5 #最大空闲数
          min-idle: 2 #最小空闲数,触发时链接数没到最大值则增加
          max-wait: 5ms #连接池最大阻塞等待时间
  ```

- 自定义String类型的序列化器

  ```java
  package com.itheima.config;
  
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.data.redis.connection.RedisConnectionFactory;
  import org.springframework.data.redis.core.RedisTemplate;
  import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
  import org.springframework.data.redis.serializer.StringRedisSerializer;
  
  /**
   * springboot帮我们创建Redistemplate的对象有两种：
   *      第一种就是RedisTemplate<Objecct,Object>，这个对象在创建的时候是没有设置序列化器。那么默认存储到redis中的时候
   *      会是jdk默认的序列化器，默认的序列化器是以字节码方式存储的。存储到Redis中的是乱码，不方便看。
   *
   *      第二种RedisTemplate<String,String>，这种key与value都设置序列号器，只不过他们序列化器，key与value都只能存放字符串类型。
   *
   *    如果你操作的时候，你的value是java对象就不能直接使用RedisTemplate<String,String>这种类型，因为你的value不是字符串类型。
   *
   *    因此需要单独自定义设置一个配置类
   *
   */
  
  @Configuration
  public class RedisConfig {
  
      @Bean
      public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
          RedisTemplate<Object, Object> template = new RedisTemplate();
          template.setConnectionFactory(redisConnectionFactory);
          template.setKeySerializer(new StringRedisSerializer()); //设置key的序列化器
          template.setValueSerializer(new GenericJackson2JsonRedisSerializer());  //设置值序列化器
          return template;
      }
  }
  ```

- 操作数据

  ```java
  @SpringBootTest
  public class RedisTemplateTest {
  
      @Autowired
      private RedisTemplate redisTemplate;
  
      @Test
      public void testString() {
          ValueOperations valueOperations = redisTemplate.opsForValue();
  
          valueOperations.set("name", "李四");
          System.out.println("name: " + valueOperations.get("name"));
      }
  
      @Test
      public void testHash() {
          //1.获取操作hash类型的客户端
          HashOperations hashOperations = redisTemplate.opsForHash();
  
          hashOperations.put("p1", "id", 101);
          hashOperations.put("p1", "name", "张三");
          hashOperations.put("p1", "age", 23);
  
          System.out.println("姓名：" + hashOperations.get("p1", "name"));
  
          System.out.println("====获取所有key====");
          Set keys = hashOperations.keys("p1");
          for (Object key : keys) {
              System.out.print(key + ",");
          }
          System.out.println();
  
          System.out.println("====获取所有value====");
          List values = hashOperations.values("p1");
          for (Object value : values) {
              System.out.print(value + ",");
          }
      }
  
      @Test
      public void testList() {
          ListOperations listOperations = redisTemplate.opsForList();
          listOperations.rightPushAll("list", "zhangsan", "lisi");
          List list = listOperations.range("list", 0, -1);
          System.out.println("list集合:" + list);
          System.out.println("size:" + listOperations.size("list"));
      }
  
      @Test
      public void testSet() {
          SetOperations setOperations = redisTemplate.opsForSet();
          setOperations.add("set", "wangwu", "wangwu", "zhaoliu");
          Set set = setOperations.members("set");
          System.out.println("set集合" + set);
  
          setOperations.remove("set", "wangwu");
          set = setOperations.members("set");
          System.out.println("set集合" + set);
      }
  
      @Test
      public void testZset() {
          ZSetOperations zSetOperations = redisTemplate.opsForZSet();
          zSetOperations.add("zset", "zhangsan", 93);
          zSetOperations.add("zset", "lisi", 94);
          zSetOperations.add("zset", "wangwu", 95);
  
          Set set = zSetOperations.range("zset", 0, -1);
          for (Object key : set) {
              System.out.println(key + "=" + zSetOperations.score("zset", key) + ",");
          }
  
          zSetOperations.incrementScore("zset", "zhangsan", 5);
          set = zSetOperations.range("zset", 0, -1);
          for (Object key : set) {
              System.out.println(key + "=" + zSetOperations.score("zset", key) + ",");
          }
      }
  
      @Test
      public void testCommon() {
          Set keys = redisTemplate.keys("*");
          System.out.println("所有的key");
          for (Object key : keys) {
              System.out.print("keys = " + key + ",");
          }
          System.out.println();
  
          System.out.println("是否存在这个key：" + redisTemplate.hasKey("p1"));
          System.out.println("这个key的类型：" + redisTemplate.type("p1"));
          redisTemplate.delete("p1");
          System.out.println("是否存在这个key：" + redisTemplate.hasKey("p1"));
      }
  }
  ```





