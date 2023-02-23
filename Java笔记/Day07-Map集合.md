#### Map集合

- 特点：

  - Map是一种键值对集合，每一个元素都包含一个键对象（Key）和一个值对象（Value）
  - 键不允许重复，值可以重复

- `HashMap<k,v>`：底层为哈希表，存取无序，需要重写键的hashcode()和equals()方法

- `TreeMap<k,v>`：底层为红黑树，可以对元素排序（自然排序Comparabler<T>、比较器排序Comparator<T>）

- Map集合基本功能

  | 方法名                                  | 说明                                  |
  | --------------------------------------- | ------------------------------------- |
  | V **put**(K key,V  value)               | 添加元素                              |
  | V **get**(Object key)                   | 根据指定的键，在Map集合中获取对应的值 |
  | V **remove**(Object key)                | 根据键删除键值对元素                  |
  | void **clear**()                        | 移除所有的键值对元素                  |
  | boolean **containsKey**(Object  key)    | 判断集合是否包含指定的键              |
  | boolean **containsValue**(Object value) | 判断集合是否包含指定的值              |
  | int **size**()                          | 集合的长度，也就是集合中键值对的个数  |

- HashMap的遍历方式

  - 键找值

    1. 用一个Set集合，获取Map中所有的键keySet()
    2. 遍历Set集合
    3. 根据键获取对应的值get(Key)

    ```java
    HashMap<String,String> map = new HashMap<>();
    
    map.put("A001","张三");
    map.put("A002","李四");
    map.put("A003","王五");
    
    Set<String> keys = map.keySet();
    for (String key : keys) {
        System.out.println(key + "--" + map.get(key));
    }
    ```

  - 键值对

    - Map中将每个键和值封装成一个个的Entry<k,v>对象，并提供getKey()和getValue()方法用于获取Entry中封装的键和值
    
    ```java
HashMap<String,String> map = new HashMap<>();
    
    map.put("A001","张三");
    map.put("A002","李四");
    map.put("A003","王五");
    
    Set<Map.Entry<String,String>> set = map.entrySet();
    
    for (Map.Entry<String,String> entry : set) {
        System.out.println(entry.getKey() + "--" + entry.getValue());
    }
    ```
    
    