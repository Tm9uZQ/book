#### XML

- XML是一种可扩展的标记语言

  - 可扩展：标签的名字是可以自定义的
  - 作用：被设计用来存储和传输数据

- 语法：

  - 文档声明格式：`<?xml version="1.0" encoding="UTF-8"?>`
  - 注释（快捷键Ctrl+/）：`<!--注释内容-->`

- 标签也称为元素

  - 由一对尖括号和合法标识符组成
  - 必须成对出现
  - 没有内容的标签可以提前结束简写成`<addesss/>`
  - 标签可以定义属性，属性和标签名空格隔开`<student id="1">`
  - 属性值必须用引号
  - 标签不能相互嵌套

- 根标签

  - 没有被其他标签包裹的标签
  - 一个XML文档中，只允许有一个跟标签

- 实体字符/转义字符

  - 以&开头；结尾 `&amp;`

- 字符数据区（快捷键CD）

  - 放在CDATA字符数据区中的数据作为纯文本解析，原样显示

    ```
    <![CDATA[
    	<&纯文本解析&>
    ]]>
    ```

- 约束

  - DTD约束缺点：

    - 不能验证数据类型
    - 本身是一个文本文件，不能验证本身是否正确

  - 格式：

    ```
    //本地约束：<!DOCTYPE 根元素 SYSTEM "DTD文件路径">
    
    //网络约束：<!DOCTYPE 根元素 PUBLIC "文件描述" "DTD文件路径">
    ```

  - Schema约束

    - 数据类型约束更完善
    - 本身是XML文件，扩展名.xsd
    - 一个XML中可以引用多个Schema文件

  - 格式：

    ```xml
    <根元素
    xmlns="命名空间"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="命名空间 schema约束文件名">
    <!-- 编写XML元素 -->
    </根元素>
    ```

    

#### dom4j

- DOM解析：一次读取XML中的所有数据，在内存中形成一颗DOM树

  ![image-20221118225112096](images/image-20221118225112096.png)

- SAXReader对象

  | 方法名                     | 说明        |
  | -------------------------- | ----------- |
  | **SAXReader**()            | 创建解析器  |
  | Document **read**(Fileurl) | 解析XML文档 |

- Document对象

  | 方法名                       | 说明       |
  | ---------------------------- | ---------- |
  | Element **getRootElement**() | 获得根元素 |

- Element对象

  | 方法名                                 | 说明                                                         |
  | -------------------------------------- | ------------------------------------------------------------ |
  | List<Element> **elements**()           | 得到当前元素下所有子元素                                     |
  | List<Element> **elements**(Stringname) | 得到当前元素下指定名字的子元素返回集合                       |
  | Element **element**(Stringname)        | 得到当前元素下指定名字的子元素,如果有很多名字相同的返回第一个 |
  | String **attributeValue**(String name) | 通过属性名直接得到属性值                                     |
  | String **elementText**(子元素名)       | 得到指定名称的子元素的文本                                   |
  | String **getName**()                   | 得到元素名字                                                 |
  | String **getText**()                   | 得到文本                                                     |

- 对于要经常得到的XML的数据，若每次解析则IO占用较高，可以把数据保存到对象中

  ```java
  //解析XML案例
  public static void main(String[] args) throws DocumentException {
      //创建解析器
      SAXReader sr = new SAXReader();
      //解析XML形成DOM树
      Document document = sr.read(new File("study_day14\\04_解析XML\\Contact.xml"));
      //获取根元素
      Element root = document.getRootElement();
      //创建存储信息的对象
      ArrayList<Contact> list = new ArrayList<>();
      //获取所有contact标签集合
      List<Element> contactList = root.elements("contact");
      for (Element contact : contactList) {
          //获取属性值
          String id = contact.attributeValue("id");
          String vip = contact.attributeValue("vip");
          //获取子标签内容
          String name = contact.element("name").getText();
          String gender = contact.element("gender").getText();
          String email = contact.element("email").getText();
  
          //存储对象信息
          Contact user = new Contact(Integer.parseInt(id),Boolean.parseBoolean(vip),name,gender,email);
          list.add(user);
      }
  
      list.forEach(System.out::println);
  }
  ```
  
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <contactList>
      <contact id="1" vip="true">
          <name>潘金莲</name>
          <gender>女</gender>
          <email>panpan@itcast.cn</email>
      </contact>
      <contact id="2" vip="false">
          <name>武松</name>
          <gender id="1000">男</gender>
          <email>wusong@itcast.cn</email>
      </contact>
      <contact id="3">
          <name>武大狼</name>
          <gender>男</gender>
          <email>wuda@itcast.cn</email>
      </contact>
      <message>
          <name>小王</name>
      </message>
      <name>老王</name>
  </contactList>
  ```
  
  

#### XPath

- 需要导入dom4j和jaxen-1.1.2.jar包

- Document中的方法

  | 方法名                               | 说明                     |
  | ------------------------------------ | ------------------------ |
  | Node **selectSingleNode**("表达式")  | 获取符合表达式的唯一元素 |
  | List<Node> **selectNodes**("表达式") | 获取符合表达式的元素集合 |

- XPath的查找方法

  | 查找方式       | 路径                  | 说明                                             |
  | -------------- | --------------------- | ------------------------------------------------ |
  | 绝对路径       | /根元素/子元素/孙元素 | 从根元素开始，一级一级向下查找，不能跨级         |
  | 相对路径       | ./子元素/孙元素       | 从当前元素开始，一级一级向下查找，不能跨级       |
  | 全文搜索       | //元素                | 找元素，无论元素在哪里，可以跳级                 |
  | 属性查询找属性 | //@属性名             | 查找属性对象，无论是哪个元素，只要有这个属性即可 |
  | 属性查找元素   | //元素[@属性名]       | 查找元素对象，全文搜索指定元素名和属性名         |
  
  ```java
  public class Demo02 {
      
      public static Document document = null;
      static {
          SAXReader sr = new SAXReader();
          try {
              //【注意点】junit的相对路径不包括模块名
              document = sr.read(new File("04_解析XML\\Contact.xml"));
          } catch (DocumentException e) {
              e.printStackTrace();
          }
      }
  
      // XPath：绝对路径
      @Test
      public void test01() {
          // 定义 XPath 表达式：/contactList/contact/name
          // 调用Document对象的selectNodes()方法执行XPath获得节点
          List<Node> nodes = document.selectNodes("/contactList/contact/name");
          for (Node node : nodes) {
              System.out.println(node.getName() + ":" + node.getText());
          }
      }
  
      // XPath：相对路径, 以调selectNodes方法用者作为参照往后找
      @Test
      public void test02() {
          // 获得根节点对象
          // 定义 XPath 表达式：./contact/name
          // 调用Document对象的selectNodes()方法执行XPath获得节点
          Element root = document.getRootElement();
          List<Node> nodes = root.selectNodes("./contact/name");
          for (Node node : nodes) {
              System.out.println(node.getName() + ":" + node.getText());
          }
      }
  
      // XPath：全文搜索
      @Test
      public void test03() {
          // 创建XPath表达式: //name
          // 调用Document对象的selectNodes()方法执行XPath获得节点
          // List<Node> nodes = document.selectNodes("//name");
          List<Node> nodes = document.selectNodes("//name");
          for (Node node : nodes) {
              System.out.println(node.getName() + ":" + node.getText());
          }
      }
  
      // XPath：属性查找   //@属性名 全文搜索属性,返回的是属性对象
      @Test
      public void test04() {
          // 创建XPath表达式: //@id 获取所有的id属性
          // 调用Document对象的selectNodes()方法执行XPath获得节点
          List<Node> nodes = document.selectNodes("//@id");
          for (Node node : nodes) {
              System.out.println(node.getName() + ":" + node.getText());
          }
      }
  
      // XPath：属性查找   //元素[@属性名] 查找具有指定属性名的元素
      @Test
      public void test05() {
          // 创建XPath表达式: //contact[@vip] 获取包含vip属性的contact元素
          // 调用Document对象的selectNodes()方法执行XPath获得节点
          List<Node> nodes = document.selectNodes("//contact[@vip='true']");
          for (Node node : nodes) {
              //System.out.println(node.getName() + ":" + node.getText());
              Element element = (Element) node;
              String str = element.elementText("name");
              System.out.println("str = " + str);
          }
      }
  }
  ```
  
  

#### 工厂模式

- 作用：解决类与类之间的耦合问题，屏蔽了外界对具体类的依赖，让类的创建更加简单
- 实现步骤：
  1. 编写一个Car接口, 提供run方法
  2. 编写一个Benz类实现Car接口,重写run方法
  3. 编写一个Bmw类实现Car接口,重写run方法
  4. 提供一个CarFactory(汽车工厂),用于生产汽车对象
  5. 在CarFactoryTes测试类使用汽车工厂创建对象



#### 动态代理

- 代理模式三要素
  - 真实对象
  - 代理对象
  - 接口：代理对象和真实对象都要实现的接口
- 优点
  - 可以在不改变方法源码的情况下，实现对方法功能的增强
  - 简化了代码
  - 提高了软件系统的可扩展性

```java
public static void main(String[] args) {
    //创建真实对象
    QQlogin qq = new QQlogin();
    //创建代理对象（记得强转为需要代理实现的接口）
    Login proxyLogin = (Login)Proxy.newProxyInstance(
            //参数1：类加载器
            QQlogin.class.getClassLoader(),
            //参数2：需要代理实现的接口
            new Class[]{Login.class},
            //参数3：执行处理器，把要增强的功能写里面
            new InvocationHandler() {
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				//Object proxy 生成的代理类的对象，不要使用
                //Method method 原目标要执行的方法
                //Object[] args 原目标要执行的方法的实参
                    System.out.println(new Date());//增强功能
                    Object result = method.invoke(qq, args);//反射调用真实对象的方法
                    System.out.println("其他操作");//增强功能
                    return result;
                }
            }
    );

    //代理对象调用方法
    proxyLogin.login();
    proxyLogin.logout();

}
```

```java
//真实类
public class QQlogin implements Login{
    @Override
    public void login() {
        System.out.println("QQ上线了");
    }

    @Override
    public void logout() {
        System.out.println("QQ下线了");
    }
}
```

```java
//接口
public interface Login {
    public abstract void login();
    public abstract void logout();
}
```

