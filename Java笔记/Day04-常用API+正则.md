#### Math类常用方法

| 方法名                                           | 说明                      |
| ------------------------------------------------ | ------------------------- |
| public static int **abs**(int a)                 | 返回参数的绝对值          |
| public static double **ceil**(double a)          | 向上取整                  |
| public static double **floor**(double a)         | 向下取整                  |
| public static int **round**(float a)             | 四舍五入                  |
| public static double **pow**(double a, double b) | 返回a的b次幂的值          |
| public static double **random**()                | 返回 [0.0 , 1.0) 的随机数 |



#### System

- 概述：
  - System位于java.lang包下，代表当前Java程序的运行平台
  - System类中提供了大量静态方法，可以获取与系统相关的信息或执行系统级操作
- 常用方法
  - 返回当前的时间（以毫秒为单位）`public static long currentTimeMillis()`
  - 实现数组拷贝`static void arraycopy(原数组,起始索引,新数组,起始索引,拷贝个数)`



#### BigDecimal

- 概述：在一些对精度要求很高的系统中，需要使用 BigDecimal 类来进行精确运算

- 创建BigDecimal对象

  - 构造方法创建
    - `BigDecimal(String val)`使用String类型的数字作为参数
    - `BigdDecimal(double val)`使用double类型的数字作为参数（不推荐）
  - 静态方法创建
    - `BigDecimal.valueOf(double val)`推荐使用

- 常用方法：

  | 方法名                                                       | 说明                           |
  | ------------------------------------------------------------ | ------------------------------ |
  | public BigDecimal **add**(另一个BigDecimal对象)              | 加法                           |
  | public BigDecimal **subtract**(另一个BigDecimal对象)         | 减法                           |
  | public BigDecimal **multiply**(另一个BigDecimal对象)         | 乘法                           |
  | public BigDecimal **divide**(另一个BigDecimal对象)           | 除法（除得尽）                 |
  | public BigDecimal **divide**(另一个BigDecimal对象，保留小数的位数，舍入模式) | 除法（除不尽）                 |
| public double **doubleValue**()                              | 转为double类型的数据，用于传参 |
  
  注：舍入模式通常使用`RoundingMode.HALF_UP`表示四舍五入



#### 基本类型包装类

- int和String相互转换

  - int转换为String
    - 字符串拼接：`5+""`
    - 使用包装类：`Integer.toString(5)`
  - String转换为int
    - 使用包装类解析字符串（parseXxx）：`Integer.parseInt("5")`
    - 使用包装类解析字符串（valueOf）：`Integer.valueOf("5")`、`Double.valueOf("5")`

- 八大数据类型都有`Xxx.toString(a)`的静态方法，将基本数据类型转换为字符串类型；除了Character之外，其他类型都有`parseXxx(String s)`的静态方法，将字符串解析为基本类型

- 装箱和拆箱

  - 装箱：基本数据类型转换为包装类的过程
  - 拆箱：包装类转换为基本数据类型的过程

  ```java
  int a = 100;
  Integer obj = Integer.valueOf(a);	//手动装箱
  int b = obj.intValue();				//手动拆箱
  Integer obj = a;					//自动装箱
  int b = obj;						//自动拆箱
  ```



#### 获取当前时间毫秒值（方法集合）

```java
// 1.System方法
long time = System.currentTimeMillis();
// 2.Date对象
Date d = new Date();
long time = d.getTime();
// 3.Calender对象
Calendar c = Calendar.getInstance();
long time = c.getTimeInMillis();
```



#### Date

| 方法名                         | 说明                                                    |
| ------------------------------ | ------------------------------------------------------- |
| public Date()                  | 构造方法：创建Date对象，代表系统当前时间                |
| public long getTime()          | Date的方法：返回当前时间的毫秒值，1970-1-1 00:00:00开始 |
| public Date(long time)         | 构造方法：把时间毫秒值转换成Date对象                    |
| public void setTime(long time) | Date的方法：时间毫秒值转换为Date对象                    |

```java
Date date = new Date();
//Thu Nov 03 11:01:30 CST 2022    CST China Standard Time
System.out.println(date); 

//获取当前时间的毫秒值 ，效果和 System.currentTimeMillis()一样
long time = date.getTime();
```



#### SimpleDateFormat工具类

- 构造器

| 构造器                                  | 说明                                     |
| --------------------------------------- | ---------------------------------------- |
| public SimpleDateFormat()               | 构造一个SimpleDateFormat，使用默认格式   |
| public SimpleDateFormat(String pattern) | 构造一个SimpleDateFormat，使用自定义格式 |

- 格式化方法

| 格式化方法                              | 说明                                    |
| --------------------------------------- | --------------------------------------- |
| public final String format(Date date)   | 将**Date**格式化为日期/时间字符串       |
| public final String format(Object time) | 将**时间毫秒值**格式化为日期/时间字符串 |

```java
//使用默认模板格式化时间
Date date = new Date();
SimpleDateFormat sf = new SimpleDateFormat();
System.out.println(sf.format(date));	//2022/11/4 上午11:41

//把Date对象格式化为指定格式的字符串
SimpleDateFormat sf2 = new SimpleDateFormat("yyyy年MM月dd日 a HH时mm分ss秒 E");
System.out.println(sf2.format(date));	//2022年11月04日 上午 11时41分14秒 周五
```



- 解析方法

| 解析方法                         | 说明                         |
| -------------------------------- | ---------------------------- |
| public Date parse(String source) | 将字符串解析为Date格式的对象 |

```java
String str2 = "2022年11月11日 11:11:11";
SimpleDateFormat sf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
Date date = sf.parse(str2);
System.out.println(date);	//Fri Nov 11 11:11:11 CST 2022
```

| 字母 |                                                              |
| ---- | ------------------------------------------------------------ |
| h    | 小时（0~12)，一般使用 hh 表示                                |
| H    | 小时（0~23)，一般使用 HH表示                                 |
| E    | 星期，用 E 表示，会根据语言环境的不同， 显示不同语言的星期几 |
| a    | 上午/下午，用 a 表示，会根据不同语言环境显示                 |



#### Calendar

- Calender是一个**抽象类**，不能直接创建对象，需要通过静态的 **getInstance()** 方法创建子类对象

  | 方法                       | 说明                       |
  | -------------------------- | -------------------------- |
  | get(int field)             | 返回指定日历字段的值       |
  | set(int field)             | 设置指定日历字段的值       |
  | add(int field, int amount) | 添加或减去某个日历字段的值 |
  | getTime()                  | 拿到此刻日期对象           |
| getTimeInMillis()          | 拿到此刻时间毫秒值         |
  
  | 字段值                 | 含义                     |
  | ---------------------- | ------------------------ |
  | YEAR                   | 年                       |
  | MONTH                  | 月（0-11表示1-12月）     |
  | DATE（或DAT_OF_MONTH） | 日                       |
  | HOUR                   | 时（12小时制）           |
  | HOUR_OF_DAY            | 时（24小时制）           |
  | MINUTE                 | 分                       |
  | SECOND                 | 秒                       |
| DAY_OF_WEEK            | 星期（1-7表示周日-周六） |
  
  ```java
  public static void main(String[] args) {
      //创建对象,获取当前时间
      Calendar c = Calendar.getInstance();
  
      //单独设置时间
      c.set(Calendar.YEAR,2021);
      c.set(Calendar.MONTH,11); //设置月份是从0开始 (设置时相当于月份减1)  11表示12月
      c.set(Calendar.DATE,20);
      //也可以一次性设置时间为 2022年12月20日
      //c.set(2022, 11, 20);
  
      //设置5天后的时间
      c.add(Calendar.DATE,5);
  
     //返回该日历时间的Date对象
     Date time = c.getTime();
     System.out.println("time = " + time);
     //返回该日历时间的毫秒值。
     long time1 = c.getTimeInMillis();
     System.out.println("time1 = " + time);
  
      showTime(c);
  }
  public static void showTime(Calendar c) {
      //get(时间单位)  获取时间
      int year = c.get(Calendar.YEAR);
      int month = c.get(Calendar.MONTH) +1; //使用0-11，代表1-12月，通常获取后加1
      int date = c.get(Calendar.DATE);
      //int hour = c.get(Calendar.HOUR);//12小时制
      int hour = c.get(Calendar.HOUR_OF_DAY);//24小时制
      int minute = c.get(Calendar.MINUTE);
      int second = c.get(Calendar.SECOND);
      System.out.println(year + "年" + month + "月" + date + "日 " + hour + "时" + minute + "分" + second + "秒");
  
      //使用1-7 表示 周日~周六
      int i = c.get(Calendar.DAY_OF_WEEK);
      String[] weeks = {"周日","周一","周二","周三","周四","周五","周六"};
      System.out.println(weeks[i-1]);
  
  }
  ```



#### JDK8开始新增日期API

- 从Java 8开始，java.time包提供了新的日期和时间API，主要涉及的类型有
  - LocalDate：不包含具体时间的日期。
  - LocalTime：不含日期的时间。
  - LocalDateTime：包含了日期及时间。
  - Instant：代表的是时间戳。
  - DateTimeFormatter：用于做时间的格式化和解析的
  - Duration：用于计算两个“时间”间隔 
  - Period：用于计算两个“日期”间隔



#### Arrays

- 数组操作工具类

- 常用API

  ```java
          int[] arr = {10, 2, 55, 23, 24, 100};
  
          // 1、返回数组内容的 toString(数组)
          String rs = Arrays.toString(arr);
  
          // 2、排序的API(默认自动对数组元素进行升序排序)
          Arrays.sort(arr);
  
          // 3、二分搜索技术（前提数组必须排好序才支持，否则出bug）
          int index = Arrays.binarySearch(arr, 55);
  
          // 在返回索引，不存在返回负数: -（应该插入的位置索引 + 1）
          int index2 = Arrays.binarySearch(arr, 22);
  ```



#### 正则表达式

- JDK查询Pattern可找到相关API

- `boolean matches(String regex)`判断字符串是否匹配指定的正则表达式

- `String replaceAll(String regex, String replacement)`将字符串中所有匹配正则表达式的内容替换成新的字符串，并返回替换后的新的字符串

- `String[] split(String regex)`根据匹配规则，把字符串分割成多个子串【用数组接收】

- 正则表达式匹配规则：

  ```
  1.范围匹配，一个[]代表一个字符
      [abc]：匹配abc中任意一个字符
      [a-z]：匹配小写字母a-z中的一个
      [A-Z]：匹配大写字母A-Z中的一个
      [0-9]：匹配数字0-9中的一个
  
      组合：
      [a-zA-Z0-9]：匹配a-z或者A-Z或者0-9之间的任意一个字符
      [a-dm-p]： 匹配a-d或m-p之间的任意一个字符
  
      排除：
      [^abc]：匹配除a、b、c之外的任意一个字符
      [^a-z]：匹配除小写字母外的任意一个字符
      
    	[a-b[m-p]]：a到b或m到p（并集）
      [a-z&&[def]]：d,e,f（交集）
      [a-z&&[^bc]]：a到z，除了b和c（[ad-z]）
      [a-z&&[^m-p]]：a到z，除了m到p（[a-lp-z]）
  ```
  
  ```
  2.预定义字符
      "." ： 匹配一个任意字符
      "\d"： 匹配一个数字字符，相当于[0-9]
      "\D"： 匹配一个非数字，相当于[^0-9]
    	"\s"： 匹配一个空白字符
      "\S"： 匹配一个非空字符
      "\w"： 匹配一个单词字符，包括大小写字母，数字，下划线，相当于[a-zA-Z0-9_]
      "\W"： 匹配一个非单词字符
  ```
  
  ```
  3.数量词（限定符），数量词要用在某个规则的后面
      ?      0次或1次
      *      0次或多次 (任意次)
      +      1次或多次
      {n}    重复n次
      {n,}   重复n次以上 (至少n次)
      {n,m}  重复n到m次（包括n和m）
      
  4.括号分组 ()
      正则表达式中用小括号()来做分组，也就是括号中的内容作为一个整体。
  ```
