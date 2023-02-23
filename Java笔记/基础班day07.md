### 面向对象基础

#### 类和对象

- 类：对现实生活中一类具有共同属性和行为的事物的抽象
- 对象：能够看得到摸得着的真实存在的实体
- <u>两者的关系：类是对象的描述，对象是类的实体</u>，在Java里面，类是对象的数据类型

#### 类

- 类的组成：<u>属性</u>和<u>行为</u>
  - 属性：在类中通过**成员变量**来体现（类中方法外的变量）
  - 行为：在类中通过**成员方法**来体现（与之前的方法相比去掉static关键字即可）

```java
public class 类名{
    //成员变量
    变量1的数据类型 变量1；
    变量2的数据类型 变量2；
    ....
    //成员方法
    方法1；
    方法2；
    ....
}
```

```java
public class Student {
    //属性：姓名、年龄
    String name;
    int age;
    
    //行为：学习
    public void study(){
        System.out.println("好好学习，争取月薪15K+");
    }
}
```

#### 对象

- 创建对象：
  - 格式：`类名 对象名 = new 类名();`
  - 范例：`Student s = new Student();`
- 使用对象：
  - 成员变量的使用
    - 格式：`对象名 变量名`
    - 范例：`s.name`
  - 成员方法的使用：
    - 格式：`对象名.方法名()`
    - 范例：`s.study();`

```java
public class StudentTest(){
    public static void main(String[] args){
        
        //对象的创建
        Student s = new Student();
        //new的数据存储在堆内存中，堆内存的数据有默认值，如果我们直接输出成员变量是不会报错的
        System.out.println(s.name);//null
        System.out.println(s.age);//0
        
        //给成员变量赋值
        s.name = "张三";
        s.age = 23;
        
        System.out.println(s.name);//张三
        System.out.println(s.age);//23
        
        //成员方法的使用
        s.study();
    }
}
```

#### 局部变量和成员变量

| 区别         | 局部变量                                       | 成员变量                                   |
| ------------ | ---------------------------------------------- | ------------------------------------------ |
| 类中位置不同 | 方法内或者方法声明上（形参）                   | 类中方法外                                 |
| 内存位置不同 | 栈内存                                         | 堆内存                                     |
| 生命周期不同 | 随着方法的调用而存在，随着方法的调用完毕而消失 | 随着对象的存在而存在，随着对象的消失而消失 |
| 初始化值不同 | 没有默认的初始化值，必须先定义后使用           | 有默认的初始化值                           |

#### private关键字

- private是一个权限修饰符
- 可以修饰成员（成员变量和成员方法）
- 被private修饰的成员<u>只能在本类中才能访问</u>

针对<u>private修饰</u>的成员变量，如果需要被其他类使用，需要提供相应的操作

- 提供“**set变量名(参数)**”方法，用于设置成员变量的值，方法用**public**修饰
- 提供“**get变量名()**”方法，用于获取成员变量的值，方法用**public**修饰



- 为了保证成员变量的有效性，通常使用private修饰变量，并提供一个**set变量名(参数)**的方法给别的类赋值

  ```java
  //封装类
  public class Student {
      //成员变量
      String name;
      private int age;
      
      //成员方法
      public void setAge(int a){
          if(a > 0 && a < 120){
              //数据是有效的
              age = a;
          }else{
              //数据有误
              System.out.println("输入的数据有误");
          }
      }
      
      public int getAge(){
          return age;
      }
      
      public void show(){
          System.out.println(name + "---" + age);
      }
  }
  ```

  ```java
  //测试类
  public class StudentTest {
      public static void main(String[] args){
          //创建对象
          Student s = new Student();
          
          //对成员变量进行赋值
          s.name = "张三";
          s.setAge(23);
          
          //获取成员变量并单独赋值
          int age = s.getAge();
          
          s.show();
      }
  }
  ```


#### this关键字

- 如果方法中和类中有相同名字的变量，用`this.变量名`代表成员变量
- 方法被哪个对象调用，this就代表哪个对象，如：Student()方法被s调用，则this代表s

```java
//封装类
public class Test{
    //成员变量
    int age = 18;
    
    public void test(){
        //局部变量
        int age = 20;
        System.out.println(age);		//20
        System.out.println(this.age);	//18
    }
}
```

```java
//封装类
public class Student {
    //私有成员变量
    private String name;
    private int age;
    
    //提供设置值的方法
    public void setName(String name){
        this.name = name;				//赋值给成员变量，this.name其实相当于s.name
    }
    public void setAge(int age){
        age = age;						//赋值给局部变量
    }
    
    //提供获取值的方法
    public String getName(){
        return name;
    }
    public int getAge(){
        return age;
    }
}
```

```java
//测试类
public class StudentTest{
    public static void main(String[] args){
        //创建对象
        Student s = new Student();
        //给成员变量赋值
        s.setName("张三");
        s.setAge(23);
        
        System.out.println(s.getName());		//张三
        System.out.println(s.getAge());		//0
    }
}
```

- 构造方法
  - 概述：
    - 创建对象时，new后面接着的`类名（）`实际上是一个方法，这个方法称之为：构造方法、构造函数、构造器；每创建一次对象都会调用一次构造方法
    - 系统会默认提供无参构造，无需手动添加；**但是如果写了有参构造，则系统不会提供无参构造，需要手动添加**
    - 利用有参构造可以在创建对象时就给成员变量进行赋值，减少set方法的使用
  - 格式：
    - 方法名和类名一致
    - 方法没有void也没有返回值类型
    - 也没有return

```java
//封装类
public class Student{
    String name;
    int age;
    
    //无参构造
    public Student(){
        
    }
    
    //有参构造
    public Student(String name,int age){
        this.name = name;
        this.age = age;
    }
    
    public Student(String name){
    this.name = name;
    }
    
    public Student(int age){
    this.age = age;
    }
}
```

```java
//测试类
public class StudentTest{
    public static void main(String[] ages){
    
    Student s = new Student("张三");
    Student s1 = new Student(23);
    Student s2 = new Student("张三",23);

	}
}
```

#### 标准封装类

```java
//封装类
public class Student{
    //1.私有成员变量
    private String name;
    private int age;
    
    //2.提供get/set方法
    public void setName(String name){
        this.name = name;
    }
    public void setAge(int age){
        this.age = age;
    }
    public String getName(){
        return name;
    }
    public int getAge(){
        return age;
    }
    
    //3.构造方法
    public Student(){
        
    }
    public Student(String name , int age){
        this.name = name;
        this.age = age;
    }
        
}
```

```java
//测试类
public class StudentTest{
    public static void main(String args){
    //创建对象
    Student s = new Student();
    //若没有进行构造方法，则需要一个个set进去
    s.setName("张三")；
    s.setAge(23);
    
    //一个个获取值
    System.out.println(s.getName() + "---" + s.getAge());
    
    //使用有参构造，可以一步到位进行赋值
    Stdent s2 = new Student("李四" , 23)；
    System.out.println(s2.getName() + "---" + s2.getAge());
	}
}
```

