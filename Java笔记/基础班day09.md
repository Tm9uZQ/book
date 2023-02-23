#### 集合

- 集合和数组的区别

  - 共同点：都是存储数据的容器
  - 不同点：数组的容量是<u>固定的</u>，集合的容量是<u>可变的</u>

  ```java
  public static void main(String[] args) {
      //创建一个集合
      ArrayList list = new ArrayList();
      //添加数据
      list.add(123);
      list.add("abc");
      System.out.println(list);   //[123, abc]
      //插入数据
      list.add(0,"插入");
      System.out.println(list);   //[插入, 123, abc]
  }
  ```

#### 泛型

-   类名<E>：<E>代表的是泛型，需要保存什么类型的数据，就把其改为对应的类型
  - `ArrayList<String> list = new ArrayList<>();		//存储字符串类型数据`

- 集合不能存储基本数据类型，只能是引用数据类型（数组、类、接口）

  | 数据类型 | 对应类名  |
  | -------- | --------- |
  | byte     | Byte      |
  | short    | Short     |
  | char     | Character |
  | int      | Integer   |
  | long     | Long      |
  | float    | Float     |
  | double   | Double    |
  | boolean  | Boolean   |

  ```java
  //Integer类
  ArrayList<Integer> list = new ArrayList<>();
  //String类
  ArrayList<String> list2 = new ArrayList<>();
  
  //需要提前创建Student类
  ArrayList<Student> list3 = new ArrayList<>();
  Student s1 = new Student("张三", 23);
  Student s2 = new Student("李四", 24);
  Student s3 = new Student("王五", 25);
  
  list.add(s1);
  list.add(s2);
  list.add(s3);
          
  //list = [Student@682a0b20, Student@3d075dc0, Student@214c265e]
System.out.println("list3 = " + list3);
  ```
  

#### ArrayList集合常用方法

| 类型 | 方法名                                   | 说明                                     |
| ---- | ---------------------------------------- | ---------------------------------------- |
| 增   | public boolean add(E element)            | 将指定的元素追加到末尾                   |
| 增   | public boolean add(int index, E element) | 在指定位置插入指定的元素                 |
| 删   | public boolean remove(Object o)          | 删除指定的元素，并返回是否成功           |
| 删   | public E remove(int index)               | 删除指定索引处的元素，并返回被删除的元素 |
| 查   | public E get(int index)                  | 返回指定索引处的元素【务必需要接收】     |
| 查   | public int size()                        | 返回集合中的元素个数                     |
| 改   | public E set(int index,E element)        | 修改指定索引处的元素，并返回被修改的元素 |

