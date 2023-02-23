#### 迭代器

- Iterator接口称为迭代器，可以实现**单列集合**元素的遍历

- 迭代器中有一个指针（变量），指向集合元素，初始值为-1

  ```java
  //遍历集合，打印奇数
  Iterator<Integer> itr = list.iterator();	//获取迭代器对象
  while (itr.hasNext()) {			//itr.hasNext()判断是否有下个元素，有返回ture
      Integer a = itr.next();		//itr.next()将指针移动到下一个位置，并获取元素
      if (a % 2 != 0) {
          System.out.println(a);
      }
  }
  
  //有bug的代码：注意next方法不要循环调用两次，会出现跳步
  while (itr.hasNext()){
      if(itr.next()%2!=0){
          System.out.println(itr.next());
      }
  }
  ```



#### 增强for循环

- 内部原理是一个Iterator迭代器

- 数组和Collection集合都可以使用，但不能为null

- 遍历过程不能增删集合元素，否则会出现并发修改异常

  ```java
  for(元素数据类型 变量名 : 数组或者Collection集合){ ... }
  ```



#### HashSet集合

- 特点：无序不重复无索引，底层是哈希表
  - 存储自定义对象时，需要重写对象的hashCode和equals方法
- 元素添加流程：
  - 先调用元素的hashCode()方法获取哈希值，确定存储位置
  - 如果存储位置为空，直接存入元素
  - 如果存储位置不为空，调用equals()方法和该位置的所有元素逐一比较，如果要存入的元素已经存在，元素将不再重复添加
- HashSet数组扩容
  - 哈希数组的**初始长度为16**
  - 当存储的元素个数超过阈值时，数组会进行扩容 （阈值 = 数组长度 * 扩容因子）
  - 默认的扩容因子是0.75，也就是当元素个数到达 16*0.75=12 时，哈希表会进行扩容，每次扩容为**原先的 2 倍**
- HashSet链表树化
  - JDK7及之前，哈希表采用**数组 + 链表**实现，从JDK8开始，哈希表优化为**数组 + 链表 + 红黑树**实现
  - 当某个**链表元素超过 8 个**，并且**数组长度>=64**时，链表会转为红黑树进行存储



#### TreeSet集合

- 不重复无索引，底层是<u>红黑树，会自动对元素进行排序</u>

- 排序规则：

  - 元素为数字时，默认按照升序排序（从小到大）
  - 元素为字符串时，按照首字符的编码值升序排序
  - 如果元素为自定义类型，需要指定排序规则

- 自然排序：

  - TreeSet集合对元素自动排序，前提是元素类实现Comparable接口

  ```java
  public class Student implements Comparable<Student>{
      private String name;
      private int age;
  
      //根据学生的年龄进行升序（从小到大）排序
      /*
          this：当前要存入的元素
          参数o：集合已经存在的元素
          返回值正数：要存入的元素比较大，存红黑树右边
          返回值负数：要存入的元素比较小，存红黑树左边
          返回值0：要存入的元素重复，不存入
       */
      @Override
      public int compareTo(Student o) {
          //return this.age - o.age; //升序排序（从小到大）
          //return o.age - this.age ; //降序排序（从大到小）
  
          //如果年龄一样，按照姓名排序
          int result = this.age - o.age;
          if(result==0){
              //根据姓名升序排序(启用字符串的默认排序规则)
              return this.name.compareTo(o.name);
          }else {
              return result;
          }
      }
  ...
  }
  ```

- 比较器排序

  - 没有实现Comparable接口的元素，无法实现自动排序；此时可以在TreeSet的构造方法中传入Comparator比较器，实现排序；同时存在时，Comparator优先级更高

    ```java
    //要求元素进行降序排序，可以在TreeSet的构造方法中，传入比较器对象（通常传匿名内部类）
    TreeSet<Integer> ts1 = new TreeSet<>(new Comparator<Integer>() {
        /*
             Integer o1 ：要存入的元素
             Integer o2 ：已经存在的元素
             正数：o1存右边
             负数：o1存左边
             返回0：不存入
         */
        @Override
        public int compare(Integer o1, Integer o2) {
            return o2 - o1; //降序
            //return o1 - o2; //升序
        }
    });
    ```



#### Collections工具类

- 在java.util包中，是一个操作集合的工具类

| 常用方法                                 | 说明                                            |
| ---------------------------------------- | ----------------------------------------------- |
| addAll(Collection<T> c,   T... elements) | 将所有指定的元素添加到指定的集合c中             |
| shuffle(List<?> list)                    | 随机打乱list集合中元素的顺序                    |
| sort(List<T> list)                       | 根据自然顺序对list集合的元素进行升序排序        |
| sort(List<T> list, Comparator<T> c)      | 根据指定的比较器,  对list集合元素进行自定义排序 |

```java
//sort(List集合)  对List集合进行默认升序排序
Collections.sort(list);
System.out.println(list);

//sort(List集合, 比较器)  使用比较器对集合进行排序
Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2 - o1; //降序
    }
});
```

