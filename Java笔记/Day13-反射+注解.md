### 反射

- Class类创建的对象我们称为Class对象/类对象/字节码对象，会保存类中的信息（构造方法、成员方法、成员变量等）

- 获取Class对象的三种方式（推荐使用第一种或第三种）

  ```java
  // 类名.class;Class<Employee>:
  Class<Employee> cls1 = Employee.class;
  
  // 对象.getClass();Class<? extends Employee>:获取当前类或者其子类的类对象
  Employee emp = new Employee();
  Class<? extends Employee> cls2 = emp.getClass();
  
  // Class.forName("类全名");包名.类名
  Class<?> cls3 = Class.forName("com.itheima.bean.Employee");
  ```

  

- 获取对象信息的方法

  ```java
  String getSimpleName();//获取类名字符串：类名
  String getName();		 //获取类全名：包名.类名
  ```



- 反射获取构造器

  | 方法                                                         | 说明                                       |
  | ------------------------------------------------------------ | ------------------------------------------ |
  | Constructor<?>[] **getConstructors**()                       | 返回所有构造器对象的数组（只能拿public的） |
  | Constructor<T> **getConstructor**(Class<?>... parameterTypes) | 返回单个构造器对象（只能拿public的）       |
  | Constructor<?>[] **getDeclaredConstructors**()               | 返回所有构造器对象的数组，存在就能拿到     |
  | Constructor<T> **getDeclaredConstructor**(Class<?>... parameterTypes) | 返回单个构造器对象，存在就能拿到           |

- Constructor类中的方法

  | 符号                                        | 说明                                       |
  | ------------------------------------------- | ------------------------------------------ |
  | T **newInstance**(Object... initargs)       | 根据指定的构造器创建对象                   |
  | public void **setAccessible**(boolean flag) | 设置为true，表示取消访问检查，进行暴力反射 |



- 反射获取成员方法

  | 方法                                                         | 说明                                         |
  | ------------------------------------------------------------ | -------------------------------------------- |
  | Method[] **getMethods**()                                    | 返回所有成员方法对象的数组（只能拿public的） |
  | Method **getMethod**(String name, Class<?>... parameterTypes) | 返回单个成员方法对象（只能拿public的）       |
  | Method[] **getDeclaredMethods**()                            | 返回所有成员方法对象的数组，存在就能拿到     |
  | Method **getDeclaredMethod**(String name, Class<?>... parameterTypes) | 返回单个成员方法对象，存在就能拿到           |

- Method类中的方法

  | 方法                                          | 说明                                                         |
  | --------------------------------------------- | ------------------------------------------------------------ |
  | Object **invoke**(Object obj, Object... args) | 调用方法，参数一：用obj对象调用该方法；参数二：调用方法的传递参数（没有就不写） |
  | public void **setAccessible**(boolean flag)   | 设置为true，表示取消访问检查，进行暴力反射                   |

  

- 反射获取成员变量

  | 方法                                    | 说明                                         |
  | --------------------------------------- | -------------------------------------------- |
  | Field[] **getFields**()                 | 返回所有成员变量对象的数组（只能拿public的） |
  | Field **getField**(String name)         | 返回单个成员变量对象（只能拿public的）       |
  | Field[] **getDeclaredFields**()         | 返回所有成员变量对象的数组，存在就能拿到     |
  | Field **getDeclaredField**(String name) | 返回单个成员变量对象，存在就能拿到           |

- Field类中的方法

  | 符号                                        | 说明                                       |
  | ------------------------------------------- | ------------------------------------------ |
  | void **set**(Object obj, Object value)      | 赋值                                       |
  | Object **get**(Object obj)                  | 获取值                                     |
  | public void **setAccessible**(boolean flag) | 设置为true，表示取消访问检查，进行暴力反射 |



- 反射应用案例

  - 通过修改简单的变量，灵活的操作对象（修改、增加）

  ```java
  // 1.通过Properties加载配置文件
  Properties pp = new Properties();
  pp.load(new FileReader("D:\\code\\javaUp182\\study_day13\\src\\config.properties"));
  // 2.得到类名和方法名 通过键找值
  String className = pp.getProperty("className");
  String methodName = pp.getProperty("methodName");
  // 3.通过类名反射得到Class对象
  Class<?> cls = Class.forName(className);
  // 4.通过Class对象创建一个对象
  Object obj = cls.getConstructor().newInstance();
  // 5.通过Class对象得到方法
  Method method = cls.getMethod(methodName);
  // 6.调用方法
  method.invoke(obj);
  ```

  

### 注解

- 自定义注解

  - 属性类型只能是：基本数据类型、String、Class、注解、枚举、以及以上类型的一维数组

  ```java
  public @interface 注解名称 {
  public 属性类型 属性名();
  }
  ```

  

- 元注解

  ```java
  @Target(ElemenType.METHOD)
  @Retention(RetentionPolicy.SOURCE)
  public @interface Override { }
  // @Target 指定注解能在哪里使用
  	TYPE			类，接口
  	FIELD			成员变量
  	METHOD			成员方法
  	PARAMETER		方法参数
  	CONSTRUCTOR		构造方法
  	LOCAL_VARIABLE  局部变量
  
  // @Retention 保留时间（生命周期）
  SOURCE			只作用于：源码阶段
  CLASS			只作用于：源码阶段、字节码阶段【默认值】
  RUNTIME			只作用于：源码阶段、字节码阶段、运行阶段
  ```

  

- 注解解析

  - AnnotatedElement接口定义了与注解解析相关的方法

  | 方法名                                                       | 说明                                                         |
  | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | T **getAnnotation**(Class<T> annotationClass)                | 根据注解类型获得对应注解对象                                 |
  | Annotation[] **getAnnotations**()                            | 获得当前对象上使用的所有注解                                 |
  | boolean **isAnnotationPresent**(Class<Annotation>annotationClass) | 判断当前对象是否使用了指定的注解，如果使用了则返回true，否则false |

  - 注解在谁头上就用谁来解析
    - 构造方法上使用Constructor来获取
    - 成员方法上使用Method来获取
    - 成员变量上使用Field来获取

- 注解解析案例

  ```java
  //注解解析案例
  public static void main(String[] args) throws NoSuchMethodException {
      //1.获取类的Class对象
      Class<Book> cls = Book.class;
      //2.获取类的方法
      Method booking = cls.getMethod("booking");
      //3.找上面的注解 运行期间获取 ，
      boolean b = booking.isAnnotationPresent(BookAnno.class);
      if (b) {
          BookAnno anno = booking.getAnnotation(BookAnno.class);
          //4.打印注解上的内容
          System.out.println(anno.name() + anno.price() +
                  Arrays.toString(anno.authors()));
      }
  }
  ```

  ```java
  //自定义类
  public class Book {
      @MyAnno4
      @BookAnno(name = "脱口秀工作手册", price = 18.8, authors = {"李诞", "王建国"})
      public void booking() {
          System.out.println("年轻人就要好好读书");
      }
  }
  ```

  ```java
  //自定义注解
  @Retention(RetentionPolicy.RUNTIME)  //源代码时期 字节码时期 运行时期存活
  public @interface BookAnno {
      public String name();
      public double price();
      public String[] authors();
  }
  ```

  



