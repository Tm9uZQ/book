#### JDBC

- Java Data Base Connectivity：Java数据库连接



#### JDBC API

```java
public static void main(String[] args) throws SQLException {
    //1、注册驱动 DriverManager：用于注册驱动
    //MySQL 5之后的驱动包，可以省略注册驱动的步骤
    DriverManager.registerDriver(new com.mysql.jdbc.Driver());
    //2、获取数据库连接 Connection：表示数据库的连接
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1?useSSL=false", "root", "S9tly");
    //3、获取执行SQL语句的对对象 Statement：执行SQL语句的对象
    Statement state = conn.createStatement();
    //4、执行SQL语句并返回结果 ResultSet：结果集
    String sql1 = "insert into st1(id,name,age) values(3,'张三',23)";
    String sql2 = "delete from st1 where id = 3";
    String sql3 = "select * from st1";
    //int row = state.executeUpdate(sql1);
    //System.out.println("成功修改" + row + "行");
    //int row = state.executeUpdate(sql2);
    //System.out.println("成功删除" + row + "行");
    //5、处理结果
    ArrayList<student> list = new ArrayList<>();
    ResultSet rs = state.executeQuery(sql3);
    //rs.next()是否有下一行
    while (rs.next()) {
        int id = rs.getInt("id");
        String name = rs.getString("name");
        int age = rs.getInt("age");
        //System.out.println("id：" + id + ",name：" + name + ",age：" + age);
        list.add(new student(id, name, age));
    }
    //6、关闭资源
    rs.close();
    state.close();
    conn.close();
    list.forEach(System.out::println);

}
```



#### JDBC 事务

```java
public static void main (String[]args){
    // 1.注册驱动(自动注册)
    Connection con = null;
    Statement state = null;
    try {
        // 2.获取连接
        con = DriverManager.getConnection("jdbc:mysql://localhost:3306/day17?useSSL=false",
                "root", "root");
        // 3.开启事务  setAutoCommit false 表示开启手动提交事务， 如果设置为true表示自动提交事务，默认是true
        con.setAutoCommit(false);
        // 4.获取到Statement
        state = con.createStatement();
        // 5.Statement执行SQL
        // 张三-500
        state.executeUpdate("update account set balance = balance - 500 where id = 1");
        //模拟一个异常
        //int a = 10/0; //计算异常
        // 李四+500
        state.executeUpdate("update account set balance = balance + 500 where id = 2");
        // 6.成功提交事务
        con.commit();
        System.out.println("成功提交事务！");
    } catch (Exception throwables) {
        // 6.失败回滚事务
        try {
            if (con != null) {
                con.rollback();
            }
            System.out.println("失败回滚事务~");
        } catch (SQLException e) {
            e.printStackTrace();
        }
        throwables.printStackTrace();
    } finally {
        try {
            if (state != null) {
                state.close();
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        try {
            if (con != null) {
                con.close();
            }
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
    }
}
```



#### SQL注入攻击

- `"SELECT * FROM user WHERE name='hehe' AND password='a'or'1'='1'"`
- Statement对象在执行sql语句时，将密码的一部分内容当做查询条件来执行了

- 解决方法：PreparedStatement预编译执行者对象

```java
public static void main(String[] args) throws SQLException {
    // 1.使用数据库保存用户的账号和密码
    // 2.让用户输入账号和密码
    Scanner sc = new Scanner(System.in);
    System.out.println("请输入账号：");
    String user = sc.next();
    System.out.println("请输入密码");
    String password = sc.next();
    // 3.使用SQL根据用户的账号和密码去数据库查询数据
    Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/day17?useSSL=false", "root", "S9tly");
    String sql = "select * from login where user=? and password=?";
    PreparedStatement state = conn.prepareStatement(sql);
    state.setString(1, user);
    state.setString(2, password);
    ResultSet rs = state.executeQuery();
    // 4.如果查询到数据，说明登录成功
    if (rs.next()) {
        System.out.println(user + "用户你好！");
    } else {
        // 5.如果查询不到数据，说明登录失败
        System.out.println("账号或密码错误！");
    }
    // 6.关闭资源
    rs.close();
    rs.close();
    conn.close();
}
```



#### 数据库连接池

- Druid是阿里巴巴开发的号称为监控而生的数据库连接池

```pascal
//druid.properties配置文件
url=jdbc:mysql://localhost:3306/day17?useSSL=false
username=root
password=S9tly
driverClassName=com.mysql.jdbc.Driver
initialSize=5
maxActive=10
maxWait=2000
```

```java
//获取连接的工具类
// 1.导入druid-1.0.0.jar的jar包
// 2.复制druid.properties文件到src下，并设置对应参数
public class DruidDataSourceUtils {
    //希望连接池在被使用的时候只加载一次
    private static DataSource ds = null;
    static{
        try (FileReader fir = new FileReader("study_day17\\src\\druid.properties")){
            // 3.加载properties文件的内容到Properties对象中
            Properties pp = new Properties();
            pp.load(fir);
            // 4.创建Druid连接池，使用配置文件中的参数
            ds = DruidDataSourceFactory.createDataSource(pp);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //对外暴露一个获取连接的方法
    //返回的结果可以尽量选择范围更大的
    //提供公共方法的时候，谁调用谁负责
    public static Connection getConection() throws SQLException {
        return ds.getConnection();
    }
}
```

```java
public static void main(String[] args) throws Exception {
    // 5.从Druid连接池中取出连接
    Connection conn = DruidDataSourceUtils.getConnection();
    // 6.执行SQL语句
    String sql = "select * from login where user=?";
    PreparedStatement pstate = conn.prepareStatement(sql);
    pstate.setString(1, "root");
    ResultSet rs = pstate.executeQuery();
    while (rs.next()) {
        int id = rs.getInt("id");
        String user = rs.getString("user");
        String password = rs.getString("password");
        System.out.println("id:" + id + ",uesr:" + user + ",password:" + password);
    }
    // 7.关闭资源
    rs.close();
    pstate.close();
    conn.close();//【注意：只是将线程还回连接池，并不是关闭】
}
```

