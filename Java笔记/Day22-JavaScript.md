### JavaScript基础语法

- JavaScript：用于制作数据验证和用户交互
  - 是一门跨平台、面向对象的脚本语言，运行在浏览器端



- 输出语句：
  - `window.alert()`写入警告框
  - `document.write()`写入HTML输出
  - `console.log()`写入浏览器控制台



- 编写位置

  - 行内位置：只能给当前标签使用

    ```html
    <!--1.行内位置, 直接写在标签的属性里面-->
    <button onclick="alert('我是内部的JavaScript代码');">点我</button>
    ```

  - 内部位置：只能给当前页面使用

    ```html
    <!--2.内部位置, 本网页有效(经常使用)-
    2.1.同一个HTML中可以有多个script标签，每个标签都会执行(从上往下运行)
    2.2.可以出现在网页中任何的位置
    		2.3type="text/javascript"可以省略
    -->
    
    <script type="text/javascript">
    document.write("<br>我是内部的JavaScript代码<br>");
    </script>
    ```

  - 外部位置：可以给多个html页面使用

    ```javascript
    document.write("<br>我是外部引入的JavaScript代码<br>")
    ```

    ```html
    <!--3.外部位置, 使用script标签引入外部的JS文件,
    src属性:指定JS文件位置
    注意:
    3.1.如果引入了外部JS文件,这个标签中的JS代码是无效的
    3.2.错误1：<script src="js/out.js" type="text/javascript">无效的js代码编写位置</script>
    3.3.错误2：<script src="js/out.js" type="text/javascript"/>不能提前结束，影响后面的JS执行
    -->
    
    <script src="js/out.js"></script>
    ```



- 定义变量
  - ES5：`var 变量名 = 变量值;`（已过时）
  - ES6：`let 变量名 = 变量值;` 或 `const 常量名 = 常量值;`



- 数据类型

  - 查询变量的所属的类型：
    - 方法1：`typeof 变量名`
    - 方法2：`typeof(变量名) `

  | 类型      | 说明                                                       |
  | --------- | ---------------------------------------------------------- |
  | number    | 数值型：包含整数、小数                                     |
  | boolean   | 布尔型：包含true/false                                     |
  | string    | 字符串：包含字符和字符串                                   |
  | object    | 对象类型：包含系统内置对象和用户自定义的对象，NULL也是对象 |
  | undefined | 未定义的类型，未知的类型 没有使用=赋值                     |



- 数据类型的boolean类转换

  | 数据类型          | 为真         | 为假     |
  | ----------------- | ------------ | -------- |
  | number            | 非0          | 0        |
  | string            | 非空字符串   | 空字符串 |
  | undefined         |              | 假       |
  | NaN(Not a Number) |              | 假       |
  | object            | 对象不为null | null     |



- 运算符
  - /：JavaScript 中除法是可以除得尽，如果除不尽会保留16位小数
  - ==：等于(比较值，不比较类型)
  - ===：恒等于（比较值和类型）
  - &和|：逻辑运算符不建议单与&、单或|，起结果false和true会变成数字0和1



- 函数

  - 命名函数

    ```java
    <script>
        /* 命名函数就是有名字的函数,格式如下:
        function 函数名(参数列表) {
            代码;
            return 返回值;
        }
        */
        // 定义一个函数实现加法功能
        function add(a, b) {
            let sum = a + b;
            return sum;
        }
    
        // 调用函数格式:
        let sum = add(10, 20);
        document.write(sum + "<br/>")
        
    </script>
    ```

  - 匿名函数

    ```java
    <script>
        /* 匿名函数格式如下:
            function (参数列表) {
                代码块;
                return 返回值;
            }
         */
    
        // 定义一个匿名函数实现加法功能
        let add = function (a, b) {
            let sum = a + b;
            return sum;
        }
    
        // 调用函数
        document.write(add(10, 20) + "<br/>")
    
    </script>
    ```



- Array
  - 定义：
    - 方法1：`let 变量名 = new Array(元素1,元素2); `
    - 方法2：`let 变量名 = [元素1,元素2]; `
  - 可以使用`push()`方法添加任意个数和类型的数据
  - 删除：`splice(开始位置, 删除的数量)`



- String
  - 定义：
    - 方法1：`let 变量名 = new String(s); `
    - 方法2：`let 变量名 = s; `
  - `trim()`去除字符串首尾的空白



- 自定义对象

  ```html
  <script>
      // JS自定义对象
      let npy = {
          name: "刘亦菲",
          age: 18,
          npy: function () {
              document.write(this.name + "是我女朋友<br/>")
          }
      }
  
      // 使用对象, 和 Java一样的格式 (点语法) 对象.成员变量  或 对象.成员方法()
      document.write(npy.name + npy.age + "<br/>")
      npy.npy()
  
  </script>
  ```



### BOM

- window浏览器窗口对象

  ```javascript
  <script>
  // window.可以省略
  // alert提示框(只有确定按钮)
  window.alert("拦截");
  
  // 输入框(可以输入数据,有确定和取消按钮), 点击确定得到输入的内容, 点击取消得到null
  document.write(window.prompt("确认继续访问？") + "<br/>")
  
  // 确认框(只有有确定和取消按钮), 点击确定得到true, 点击取消得到false
  document.write(window.confirm("你成年了吗？") + "<br/>")
  
  </script>
  ```

- 计时器相关

  ```javascript
      // 定时器
      // window.setTimeout(函数名, 间隔毫秒数) 过指定时间后执行一次函数
      setTimeout(refresh,2000);
  
      // window.setInterval(函数名, 间隔毫秒数) 每隔指定的时间都会执行前面的函数(反复执行)
      setInterval(refresh,2000);
  ```

- window.location地址栏对象

  ```javascript
      // 获取当前地址
      document.write(location.href + "<br/>")
  
      // 跳转到新设定的url地址
      location.href = "https://none.org.cn";
  ```

- window.history历史记录

  - 当页面进行了跳转时，有了缓存记录该功能才能使用

  ```javascript
  <button onclick="history.back()">后退</button>
  <button onclick="history.forward()">前进</button>
  ```

  

### DOM

| 获取元素的方法                               | 作用                         |
| -------------------------------------------- | ---------------------------- |
| document.**getElementById**("b1")            | 通过id获取一个元素           |
| document.**getElementsByTagName** ("标签名") | 通过标签名获取**一组**元素   |
| document.**getElementsByName**("name")       | 通过name属性获取**一组**元素 |
| document.**getElementsByClassName**("类名")  | 通过样式类名获取**一组**元素 |



### 事件监听

- 用户可以对网页的元素有各种不同的操作如：单击，双击，鼠标移动等这些操作就称为事件

  ```html
  <body>
  <!--命名函数设置事件-->
  <input type="button" value="命名函数设置事件" onclick="abc()"/>
  <br/>
  
  <!--匿名函数设置事件
  注意:标签要放在事件前面,才能通过id找到标签-->
  <input id="input2" type="button" value="匿名函数设置事件"/><br/>
  
  <script>
      function abc() {
          alert("命名函数设置事件");
      }
  
      document.getElementById("input2").onclick = function () {
          alert("匿名函数设置事件");
      }
  </script>
  </body>
  ```

  ```html
  <body>
  <!--onfocus: ?-->
  用户名: <input id="in1" name="user" onclick="chick()">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span id="sp1">请输入</span>
  
  <script>
      // 命名函数设置事件
      function chick() {
          document.getElementById("sp1").innerText = "正在输入"
      }
  
      // 匿名函数设置失去焦点的事件
      document.getElementById("in1").onblur = function () {
          document.getElementById("sp1").innerText = "请输入"
      }
  </script>
  </body>
  ```



### JavaScript&正则表达式

- 创建方式：

  - `let reg = new RegExp("1[3456789]\\d{9}");`字符串内的斜杠\想要进行转义
  - `let reg = /^1[3456789]\d{9}$/;`在js中正则表达式默认是部分匹配，如需精确匹配需要在首尾添加^和$

  ```javascript
  //定义正则表达式
  let reg = /^[1][3456789]\d{9}$/;
  //test(需要匹配的字符串)
  let result = reg.test(phone);
  ```

  

