- 相关实体类

  - 【注意：定义实体时，基本类型应选用包装类；如果不使用包装类，初始化对象后会有一个默认值，在update修改数据时，会影响null的判断】
  
  ```java
  //封装User表的数据
  public class User {
      private Integer id;
      private String username;
      private Date birthday;
      private String sex;
      private String address;
  
      //关联userinfo对象，作为属性放进来
      private UserInfo userInfoDate;
  
      //关联order对象，作为属性放进来
      //因为是一对多，所以封装为list对象
      private List<Order> userOrderList;
      
      //关联Role的对象,作为属性放进来
      //因为是一对多，所以封装成list对象
      private List<Role> roleList;
....
  ```
  
  ```java
  //封装userinfo表的数据
  public class UserInfo {
      private Integer id;
      private Double height;
      private Double weight;
      private Integer married;
....
  ```
  
  ```java
  //封装order表的数据
  public class Order {
      private Integer oId;
      private Integer userId;
      private String number;
      private Timestamp createTime;
      private String note;
....
  ```
  
  ```java
  //封装role表的数据
  public class Role {
      private Integer roleId;
      private String roleName;
      private String roleDetail;
  
      //因为role与user是一对多的关系，所以封装为list对象
      private List<User> userList;
  ....
  ```



#### 多表查询

- 一对一关联

  ```java
  //根据用户ID查询用户信息
  List<User> findUserById(int id);
  ```

  ```xml
  	<!--
       多表查询时，即使类的成员变量名和数据库字段名一致，也不会完成自动映射，需要手写对应关系
       association标签：配置一对一的关系
       collection标签：配置一对多的关系
       -->
  
      <resultMap id="UserMap" type="User">
          <id column="id" property="id"/>
          <result column="username" property="username"/>
          <result column="birthday" property="birthday"/>
          <result column="sex" property="sex"/>
          <result column="address" property="address"/>
          <!-- association：一对一的映射 -->
          <association property="userInfoDate" resultMap="InfoMap"/>
      </resultMap>
  
      <resultMap id="InfoMap" type="UserInfo">
          <id column="id" property="id"/>
          <result column="height" property="height"/>
          <result column="weight" property="weight"/>
          <result column="married" property="married"/>
      </resultMap>
  
      <select id="findUserById" resultMap="UserMap">
          select * from user inner join
          user_info
          on user.id=user_info.id
          where
          user.id=#{id};
      </select>
  ```

  

- 一对多的关联

  ```java
  //根据用户ID查询用户信息及其订单
  User findUserAndOrder(int id);
  ```

  ```xml
  <resultMap id="UserMap" type="User">
      <id column="id" property="id"/>
      <result column="username" property="username"/>
      <result column="birthday" property="birthday"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>
      <!--<association property="userInfoDate" resultMap="InfoMap"/>-->
      <collection property="userOrderList" resultMap="OrderMap"/>
  </resultMap>
  
  <resultMap id="OrderMap" type="Order">
      <id column="oid" property="oId"/>
      <result column="user_id" property="userId"/>
      <result column="number" property="number"/>
      <result column="create_time" property="createTime"/>
      <result column="note" property="note"/>
  </resultMap>
  
  <select id="findUserAndOrder" resultMap="UserMap">
      select * from user left outer join
      tb_order
      on user.id=tb_order.user_id
      where
      user.id=#{id};
  </select>
  ```



- 多对多关联

  ```java
  //根据用户id查询用户关联的角色信息
  User findUserAndRoles(int id);
  ```

  ```xml
  <resultMap id="UserMap" type="User">
      <id column="id" property="id"/>
      <result column="username" property="username"/>
      <result column="birthday" property="birthday"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>
      <!--<association property="userInfoDate" resultMap="InfoMap"/>-->
      <!--<collection property="userOrderList" resultMap="OrderMap"/>-->
      <collection property="roleList" resultMap="RoleMap"/>
  </resultMap>
  
  <resultMap id="RoleMap" type="Role">
      <id column="role_id" property="roleId"/>
      <result column="role_name" property="roleName"/>
      <result column="role_detail" property="roleDetail"/>
  </resultMap>
  
  <select id="findUserAndRoles" resultMap="UserMap">
      select * from user
          inner join user_role on user.id=user_role.user_id
          inner join role on user_role.role_id=role.role_id
      where user.id = #{id}
  </select>
  ```

  ```java
  //根据角色id查询角色信息以及相关的用户信息
  Role findRoleAndUsers(int id);
  ```

  ```xml
  <resultMap id="RoleMap2" type="Role">
      <id column="role_id" property="roleId" />
      <result column="role_name" property="roleName" />
      <result column="role_detail" property="roleDetail" />
      <collection property="userList" resultMap="UserMap2"/>
  </resultMap>
  
  <resultMap id="UserMap2" type="User">
      <id column="id" property="id"/>
      <result column="username" property="username"/>
      <result column="birthday" property="birthday"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>
  </resultMap>
  
  <select id="findRoleAndUsers" resultMap="RoleMap2">
      select * from role
          inner join user_role on role.role_id=user_role.role_id
          inner join user on user_role.user_id=user.id
      where role.role_id = #{id}
  </select>
  ```



#### 级联查询

- 在xml里将若干个子查询进行级联，先查询出一部分数据，再做另一部分的查询

  - 在调用findUserById2方法时，会对User的基本信息进行查询，但是User类中除了包含基本信息外，还有userInfoDate、userOrderList等数据需要确定，如果resultMap中有对这些数据配置查询方法，则会通过配置的findUserInfoById2、findOrderByUserId2方法传递相关参数进行后续查询
  - resultMap中的一些参数
    - `select="方法名"`第二个查询的方法
    - `column="id"`传递给第二个查询的参数
    - `fetchType="lazy"`懒加载，使用到的时候在进行第二个查询语句
  
  ```java
  //级联查询
  //根据用户ID查询用户的基本信息
  User findUserById2(int id);
  //根据用户ID查询用户的扩展信息
  UserInfo findUserInfoById2(int id);
  //根据用户ID查询用户的订单信息
  List<Order> findOrderByUserId2(int id);
  ```

  ```xml
  <!-- 级联查询 -->
  <resultMap id="UserMap3" type="User">
      <id column="id" property="id"/>
      <result column="username" property="username"/>
      <result column="birthday" property="birthday"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>
      <association property="userInfoDate" select="findUserInfoById2" column="id" fetchType="eager"/>
      <collection property="userOrderList" select="findOrderByUserId2" column="id" fetchType="lazy"/>
  </resultMap>
  
  <select id="findUserById2" resultMap="UserMap3">
      select * from user where id=#{id}
  </select>
  <select id="findUserInfoById2" resultType="cn.org.none.pojo.UserInfo">
      select * from user_info where id=#{id}
  </select>
  <select id="findOrderByUserId2" resultType="cn.org.none.pojo.Order">
      select * from tb_order where user_id=#{id}
  </select>
  ```



- 但在开发中常用的是在方法中进行子查询嵌套

![image-20221127145030637](images/image-20221127145030637.png)



#### 注解开发

- 注解开发可以在接口文件中完成简单功能，无需在映射文件中编写

  ```java
  //注解开发
  //查询所有的User的数据
  @Select("select * from user")
  List<User> selectAllUsers3();
  
  //新增User对象
  @Insert("insert into user values (null,#{username},#{birthday},#{sex},#{address})")
  int insertUser3(User user);
  
  //修改User对象地址
  @Update("update user set address=#{address} where id =#{id}")
  int updateUser3(User user);
  
  //删除User对象
  @Delete("delete from user where id=#{id}")
  int deleteUser3(int id);
  ```



#### MyBatis缓存

- MyBatis框架中缓存分为一级缓存和二级缓存，通过缓存策略可以减少查询数据库的次数，提升系统性能

- 一级缓存
  - 默认开启
  - 在同一个sqlSession范围内部有效
  - 当调用sqlSession的修改、添加、删除、提交、关闭等方法时，一级缓存会被清空；也可以手动调用`sqlSession.clearCache()`进行清理
- 二级缓存
  - 开启条件
    - 在实例类中实现Serializable接口，如果涉及多表查询，则每个实体类都需要实现这个序列化
    - 在mybatis-config.xml的setting里面需要设置开启二级缓存：`<setting name="cacheEnabled" value="true"/> `
    - 在Mapper.xml映射文件的<mapper>标签中添加`<cache/>`标签，开启二级缓存使用
  - 存在Mapper中，在多个sqlSession中共享
  - 只有在前一个sqlSession中调用close()后才会缓存

