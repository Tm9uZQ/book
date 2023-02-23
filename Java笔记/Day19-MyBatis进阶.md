#### Mybatis

- 复制模块需要更改的三个地方

  ![image-20221125210716110](images/image-20221125210716110.png)



#### 增删查改

- 参数占位符

  - `#{}`先使用?占位，执行sql时将具体值赋值给?
    - 底层使用的是PreparedStatement，不会出现sql注入问题；并且可以设置预编译，提高sql执行效率
  - `${}`拼接sql，会存在sql注入的问题
    - 底层使用的是Statement，可能出现sql注入问题

- SQL语句中特殊字符处理

  - 转义字符
  - `<![CDTA[内容]]>`

- 对数据库进行**增删改**操作时需提交事务

  - 手动提交事务`sqlSession.commit();`
  - 自动提交事务`sqlSession=factory.openSession(true);`

- 添加数据时获取新增的主键值

  - `useGeneratedKeys="true"`使用MySQL生成的主键
  - `keyProperty="属性"`实体类中对应的属性

- 增删查改对应的标签

  | **标签** | **说明**          |
  | -------- | ----------------- |
  | <select> | 编写查询的SQL语句 |
  | <insert> | 编写添加的SQL语句 |
  | <update> | 编写修改的SQL语句 |
  | <delete> | 编写删除的SQL语句 |

```java
//UserMapper.java文件
//查询所有的用户
List<User> findAllUsers();

//查询单个用户
User selectUser(int id);

//删除单个用户
void deleteUser(int id);

//修改用户信息
int updateUser(User user);

//添加用户
int addUser(User user);
```

```xml
<!-- UserMapper.xml文件 -->
<!--mapper namespace="包名.接口名"-->
<mapper namespace="cn.org.none.mapper.UserMapper">
    <!--select id="方法名" resultType="方法返回值类型"-->
    <select id="findAllUsers" resultType="cn.org.none.pojo.User">
        <!-- SQL语句 -->
        select * from user;
    </select>

    <select id="selectUser" resultType="cn.org.none.pojo.User">
        select
        <include refid="userSql"></include>
        from user where id = #{id};
    </select>

    <sql id="userSql">
        id,username,birthday,sex,address
    </sql>

    <delete id="deleteUser">
        <![CDATA[
        delete from user where id = #{id};
        ]]>
    </delete>

    <update id="updateUser">
        <![CDATA[
        update user set
        username=#{username},
        birthday=#{birthday},
        sex=#{sex},
        address=#{address}
        where
        id = #{id};
        ]]>
    </update>

    <insert id="addUser" useGeneratedKeys="true" keyProperty="id">
    <![CDATA[
    insert into user
    values
    (null,#{username},#{birthday},#{sex},#{address});
    ]]>
    </insert>
```

```java
//测试文件
public class TestMybatis {

@Test
public void Test01() throws IOException {
    //1.在测试案例里面访问resoureces的资源，可以直接访问文件
    String resource = "mybatis-config.xml";
    //2.读取核心配置文件信息，生成一个输入流
    InputStream inputStream = Resources.getResourceAsStream(resource);
    //3.创建一个生成SqlSession的工厂类 类似于连接池
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    //4.获取sqlsession 建立连接
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //5.获取执行对象
    UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
    //6.调用方法
    List<User> allUsers = userMapper.findAllUsers();
    //7.关闭资源
    sqlSession.close();
}

@Test
public void Test02() throws IOException {

    //6.调用方法
    userMapper.selectUser(1);
    //7.关闭资源
    sqlSession.close();
}

@Test
public void Test03() throws IOException {

    //6.调用方法
    userMapper.deleteUser(3);
    //7.提交事务
    sqlSession.commit();
    //8.关闭资源
    sqlSession.close();
}

@Test
public void Test04() throws IOException {

    //6.调用方法
    User user = new User(4, "牛魔王", Date.valueOf("1980-1-1"), "男", "太乙江山");
    int row = userMapper.updateUser(user);
    System.out.println("row = " + row);
    //7.提交事务
    sqlSession.commit();
    //8.关闭资源
    sqlSession.close();
}

@Test
public void Test05() throws IOException {

    //6.调用方法
    User user = new User(10086, "女菩萨", Date.valueOf("2000-1-1"), "女", "观音山");
    System.out.println("插入前的ID = " + user.getId());
    int row = userMapper.addUser(user);
    System.out.println("row = " + row);
    System.out.println("插入后的ID = " + user.getId());
    //7.提交事务
    sqlSession.commit();
    //8.关闭资源
    sqlSession.close();
}
```



#### 多参数处理

- 关于@Param注解
  - 如果mapper接口方法上**只有一个普通类型的参数**，不需要加@Param注解，映射配置文件中#{}中**可以任意写名称**，建议做到见名知意
  - 如果mapper接口方法上是**实体类类型的参数**，不需要加@Param注解，映射配置文件中#{}中**和实体类中的属性名保持一致**
  - 如果mapper接口方法上是**多个参数**，需要加@Param注解给每个参数起名字，映射配置文件中**#{}中书写的是@Param注解中写的名称**
  - 如果mapper接口方法是**单个单列集合类型或者数组类型**，需要加@Param注解给参数起名
  - 如果mapper接口方法是**单个map集合类型**，不需要加@Param注解，映射配置文件中#{}中名称需要**和map集合的键名保持一致**

```java
//多参数处理(默认方案需要对SQL语句进行修改，可读性差)
//List<User> selectByCondition(String name,String sex);
//方案1：散装处理 @Param(“占位符”)
//List<User> selectByCondition(@Param("username")String username, @Param("sex")String sex);
//方案2：对象参数 对象的属性名称要和参数占位符名称一致
//List<User> selectByCondition(User user);
//方案3：Map集合 Map的键要和占位符名称一致
List<User> selectByCondition(Map<String, Object> map);
```

```xml
<select id="selectByCondition" resultType="cn.org.none.pojo.User">
    <![CDATA[
	select * from user where username like #{username} and sex = #{sex};
	]]>
    <!-- 默认解决方案：arg系列从0开始，param系列从1开始 -->
    <!-- select * from user where username like #{arg0} and sex = #{arg1};-->
    <!-- select * from user where username like #{param1} and sex = #{param2};-->
</select>
```

```java
@Test
public void Test06() throws IOException {

    //6.调用方法
    //默认方案&方案1
    //List<User> userList = userMapper.selectByCondition("%精%", "女");
    //方案2
    //User user = new User();
    //user.setUsername("%女%");
    //user.setSex("女");
    //List<User> userList = userMapper.selectByCondition(user);
    //方案3
    Map<String,Object> map = new HashMap<>();
    map.put("username","%女%");
    map.put("sex","女");
    List<User> userList = userMapper.selectByCondition(map);
    userList.forEach(System.out::println);
    //7.关闭资源
    sqlSession.close();
}
```



#### 动态SQL

#### 多条件查询

- where标签
  - 自动补全where关键字
  - 去掉多余的and和or关键字
- if标签
  - 属性内的条件成立时，内容则拼接为SQL语句

```java
//多条件查询
List<User> selectIf(@Param("username") String username, @Param("sex") String sex);
```

```xml
<select id="selectIf" resultType="cn.org.none.pojo.User">
    select * from user
    <where>
        <if test="username != null and username !=''">
            username like #{username}
        </if>
        <if test="sex != null and sex != ''">
            and sex = #{sex}
        </if>
    </where>

</select>
```

```java
@Test
public void Test07() throws IOException {

    //6.调用方法
    List<User> userList = userMapper.selectIf("%精%","女");
    userList.forEach(System.out::println);
    //7.关闭资源
    sqlSession.close();
}
```



#### 修改部分字段数据

- set标签
  - 用在update语句中，相当于set关键字
  - 可去掉SQL语句中多余的逗号
- 注意：
  - 当所有参数都为空时，SQL语句会报错，因此需要添加必要参数防止报错
  - where语句要在<set>标签外面

```java
//修改部分数据
int updateIf(User user);
```

```xml
<update id="updateIf">
    update user
    <set>
        <!--添加无用字段用于解决空参报错-->
        id = #{id},
        
        <if test="username != null and username != ''">
            username=#{username},
        </if>
        <!-- 日期格式的数据不能和字符串类型作比较 -->
        <if test="birthday != null">
            birthday=#{birthday},
        </if>
        <if test="sex != null and sex != ''">
            sex=#{sex},
        </if>
        <if test="address != null and address != ''">
            address=#{address}
        </if>
    </set>
    where id=#{id}
</update>
```

```java
@Test
public void Test08() throws IOException {

    //6.调用方法
    User user = new User();
    user.setUsername("狐狸精");
    user.setBirthday(Date.valueOf("1999-1-1"));
    user.setId(2);
    int row = userMapper.updateIf(user);
    System.out.println("row = " + row);
    //提交事务
    sqlSession.commit();
    //7.关闭资源
    sqlSession.close();
}
```



#### 批量删除

- foreach标签

  | foreach标签的属性 | 作用                           |
  | ----------------- | ------------------------------ |
  | collection        | 参数名                         |
  | item              | 设置变量名，代表每个遍历的元素 |
  | separator         | 遍历一个元素添加的内容         |
  | #{变量名}         | 先使用?占位, 后面给?赋值       |
  | open              | 在遍历前添加一次字符           |
  | close             | 在遍历后添加一次字符           |

```java
//批量删除 collection标签属性默认值是数组叫array，集合叫list
//如果要自定义，则手动进行散装
int deleteIf(@Param("list") int[] ids);
```

```xml
<delete id="deleteIf">
    delete from user where id in
    <foreach collection="list" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</delete>
```

```java
@Test
public void Test09() throws IOException {

    //6.调用方法
    int[] list = {2,4};
    int row = userMapper.deleteIf(list);
    System.out.println("row = " + row);
    //提交事务
    sqlSession.commit();
    //7.关闭资源
    sqlSession.close();
}
```



#### 根据性别查询用户

- choose标签

  | choose标签的属性 | 作用                 |
  | ---------------- | -------------------- |
  | when             | 匹配一个条件         |
  | otherwise        | 所有条件不匹配时执行 |

```java
//根据数字判断性别进行查询
List<User> selectSex(int sex);
```

```xml
<select id="selectSex" resultType="cn.org.none.pojo.User">
    select * from user
    <where>
        <choose>
            <when test="sex == 1">
                sex = '男'
            </when>
            <when test="sex == 0">
                sex = '女'
            </when>
            <otherwise>
                sex = '女'
            </otherwise>
        </choose>
    </where>
</select>
```

```java
@Test
public void Test10() throws IOException {

    //6.调用方法
    List<User> users = userMapper.selectSex(10);
    users.forEach(System.out::println);
    //7.关闭资源
    sqlSession.close();
}
```



#### 接口映射文件：resultMap输出映射

- 当字段名与对象的成员变量一致时,MyBatis可以把查询的结果自动封装为对象；但是不一致时，名称不一致的成员变量就获取不到数据

- 解决方法：

  - 方案1：给无法直接对应的字段赋予别名
  
    ```xml
    <select id="findAllOrders" resultMap="OrderMap">
         select 
        	o_id oId,
        	user_id userId,
        	number,
        	create_time createTime,
         	note 
        	from tb_order;
    </select>
    ```
  
  - 方案2：在`mybatis-config.xml`配置文件的`<settings>`标签内添加字段进行设置
  
    ```xml
    <settings>
    	<!-- 使用全局配置，配置数据库下划线的字段自动转换为小驼峰命名 -->
    	<setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    ```
  
  - 方案3：resultMap输出映射（接口映射文件）
  
    ```xml
    <mapper namespace="cn.org.none.mapper.OrderMapper">
    
        <!-- 使用resultMap做映射 -->
        <!--
        id属性：表示唯一标识，通过id找到对应的resultMap
        type属性：要封装的对象
        id标签：用来做主键字段的映射
        column属性：数据库的字段名
        property属性：类的成员变量名
        result标签：配置主键之外的其他字段
        -->
        <resultMap id="OrderMap" type="Order">
            <id column="o_id" property="oId"></id>
            <result column="user_id" property="userId"></result>
            <result column="creat_time" property="createTime"></result>
        </resultMap>
      <select id="getAllOrder" resultMap="OrderMap">
            select * from tb_order;
        </select>
    </mapper>
    ```
  
    



