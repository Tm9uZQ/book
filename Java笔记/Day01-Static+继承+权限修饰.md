#### static静态关键字

- 静态成员变量（类变量），可以被类的所有对象共享（访问、修改）
  - 访问格式：`类名.静态成员变量`（推荐）、`对象.静态成员变量`（不推荐）
  - 属于类、加载一次，内存中只有一份
  - 出现顺序：**类加载 --> 静态变量创建 --> 创建对象 --> 成员变量**
- 静态方法（类方法）
  - 访问格式：`类名.静态成员方法`（推荐）、`对象.静态成员方法`（不推荐）
  - 静态方法只能访问本类的静态成员，静态方法中**不能使用super、this**关键字
- 静态方法应用-工具类
  - 工具类不需要创建对象，其**构造器私有化**处理
- 静态方法应用-静态代码块
  - 代码块是类的5大成分之一（成员变量、构造器，方法，代码块，内部类）
  - 静态代码块`static{ ... }`执行时间：<u>静态代码块随着类的加载而执行，只执行一次</u>
    - **触发类加载的情况**：1、首次创建对象；2、调用类的方法；3、在类中运行main方法
  - 构造代码块`{ ... }`执行时间：构造代码块随着构造方法的调用而先执行，每次调用构造方法时，构造代码块都会执行



#### 继承

- 定义：继承是将多个类的相同属性和行为抽取到单独一个类中，那么多个类无需再定义这些属性和行为，只要继承单独这个类即可使用这些属性和行为了。多个类称为**子类（派生类）**，单独的这个类称为**父类(基类 或超类)**

- 格式：`public class 子类名 extends 父类名 {  }`

- 特点：
  - Java类只支持**单继承**，不支持多继承，但支持**多层继承**
  - 子类可以继承父类的私有成员，但是子类不能直接访问
  - 子类<u>不可以继承父类的构造方法</u>（构造方法名必须和类名相同）
  
- 内存结构：子类对象的内存空间中存在父类空间和子类空间，子类对象创建时，会先完成父类空间的初始化（默认先执行父类的无参构造方法，<u>在子类的所有构造方法的第一行默认有super()</u>，表示调用父类的无参构造方法）

- **super(...)与this(...)**：可以用来访问父类/本类的构造方法

  ```java
  public User(String name, int age){
      this.name = name;
      this.age = age;
  }
  
  public User(String name, int age, String school){
      this(name,age);			//调用本类的两个参数的构造方法，降低代码的重复率
      this.school = school;
  }
  ```



#### 方法重写

- 方法名、参数列表、返回值类型都保持不变
  - <u>注意点：返回值类型通常保持一致，基本数据类型不能改变，但引用数据类型可以改为比原方法更小的类（包含关系）</u>
- 子类重写后的方法，访问权限要大于等于父类方法的权限。（权限从小到大： private、缺省不写、protected、public）
  - protected权限修饰符：在不同包的子类中只能通过子类对象去访问
- 私有方法和静态方法不能被重写



#### 权限修饰符

| 修饰符/作用范围 | 同一个类中 | 同一个包中其他类 | 不同包下的子类 | 不同包下的无关类 |
| :-------------: | :--------: | :--------------: | :------------: | :--------------: |
|     private     |     ✓      |                  |                |                  |
|      缺省       |     ✓      |        ✓         |                |                  |
|    protected    |     ✓      |        ✓         |       ✓        |                  |
|     public      |     ✓      |        ✓         |       ✓        |        ✓         |

```java
public class Zi extends Fu {
    public static void main(String[] args){
        Zi zi = new Zi();
        zi.protectedMethod();	//注：protected不同包下的之类可以访问，是指可以通过继承了父类的该子类对象进行访问
    }
}
```

