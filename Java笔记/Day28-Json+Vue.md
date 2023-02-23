#### ES6模板字符串

- ES6模板字符串: `${变量名}`

  ```html
  <script>
      let n1 = 4;
      let n2 = 5;
      let result = n1 + n2;
      document.write(`${n1}+${n2}=${result}`+"<br/>")
  
      let person={
          name:"张三",
          age:23,
          eat:function () {
              document.write(`${this.name},正在吃饭`+"<br/>")
          }
      }
      document.write(`姓名：${person.name}，年龄${person.age}`+"<br/>")
      person.eat()
  </script>
  ```

  

#### JSON

- JavaScript Object Notation：JavaScript对象表示方法，就是把js对象变成字符串

- JSON比xml体积更小，更快、更易解析

- 网络传输使用JSON字符串

  - 服务端处理请求数据：JSON字符串转为java对象
  - 服务端发送响应数据：Java对象转为JSON字符串

- Axios中，JSON字符串和JS对象会自动进行转换

- js对象转换为字符串

  - 最外面加单引号'包住'
  - 键加双引号"包住"
  - 所有数据放在一行

- js对象和json相互转换的方法

  - `stringify(js对象)`将对象转换为json格式字符串
  - `parse(字符串)` 将json格式字符串解析成对象

  ```html
  <script>
      let person = {
          name: "lisi",
          age: 24
      };
  
      //手动将js对象转换为json字符串
      let JsonStr1 = '{"name":"lisi", "age":24}';
      document.write(JsonStr1 + "<br/>");
  
      //使用stringify方法，js2json
      let JsonStr2 = JSON.stringify(person);
      document.write(JsonStr2 + "<br/>");
  
      //使用parse方法，json2js
      let person2 = JSON.parse('{"name":"wangwu", "age":25}');
      document.write(person2 + "<br/>");  // [object Object] 这是JS对象的默认打印
      document.write(`姓名：${person2.name}，年龄：${person2.age}` + "<br/>")  //姓名：wangwu，年龄：25
  </script>
  ```

  

#### Java中的JSON转换工具

- Fastjson：阿里巴巴提供的一个高性能json转换工具

- 在pom.xml中导入坐标

  ```xml
  <dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.76</version>
  </dependency>
  ```

- Java对象转JSON字符串：

  ```java
  String user2json = JSON.toJSONString(user);
  ```

- JSON字符串转Java对象：

  ```java
  String jsonStr = "{\"id\":1,\"name\":\"lisi\",\"password\":\"1234\"}";
  User user1 = JSON.parseObject(jsonStr, User.class);
  ```

  

#### Vue

- Vue是一套前端框架，免除原生JavaScript中的DOM操作,简化书写
- 基于MVVM（Model-View-ViewModel）思想，实现数据的双向绑定



#### Vue快速入门

- 在HTML页面引入Vue.js文件

  ```html
  <script src="js/vue.js"></script>
  ```

- 在body区域编写视图

  ```html
  <!--2.编写视图
      {{username}}: 插值运算,会显示username的具体值
  -->
  <div id="app">
      {{username}}
  </div>
  ```

- 在js代码区域，创建Vue核心对象，进行数据绑定

  - el选择器：【绑定视图】用于指定视图区域，此区域下的所有表达式、事件等内容都会受到vue控制
  - data：【初始化数据】用于初始化当前vue对象中的数据
- methods：【定义方法】方法可以直接通过对象名调用，也可以在方法内部通过this调用
  
  ```html
  <script>
      // 3.创建Vue核心对象, 提供模型数据
      // el:【绑定视图】 "#app" 使用id="app"的标签作为vue的视图
      new Vue({
          el: "#app",
          data() {
              return {
                  username: "张三"
              }
          }
      });
  </script>
  ```



#### Vue的相关设置

- IDEA中添加vue语法提示
- Chrome安装vue开发插件



#### 常用指令

- 文本插值：v-text、v-show

  ```html
  <body>
      <div id="div">
  	    <!--方式1：{{变量名}} 插入值表达式，标签体绑定文本字符串，类似于innerText的功能-->
          <!--方式2：v-text="变量名"，与{{变量名}}功能一样-->
          <!--方式3：v-html="变量名"，标签体绑定html代码字符串，类似于innerHTML的功能-->
          
          <div>方式1：<span>{{msg}}</span></div>
          <div>方式2：<span v-text="msg"></span></div>
          <div>方式3：<span v-html="msg"></span></div>
      </div>
  </body>
  <script>
      new Vue({
          el:"#div",
          data:{
              msg:"<h1>hello Vue</h1>"
          }
      });
  </script>
  ```


- 条件渲染：v-if和v-show

  ```html
  <body>
  <div id="div">
  <!--
  v-if和v-show的区别
  	v-if：
  		如果条件为false，页面中不会存在该元素
  		在状态切换情况较少的情况下使用（在频繁切换状态时还要临时去找其他值）
  	v-show：
  		如果条件为false，页面中有这个元素，只不过其display属性值为none
  		在状态切换频繁的情况下使用
  -->
      <div v-if="score=='A'">优秀</div>
      <div v-else-if="score=='B'">良好</div>
      <div v-else-if="score=='C'">及格</div>
      <div v-else="score=='D'">不及格</div>
  
      <hr/>
  
      <div v-show="score=='A'">优秀</div>
      <div v-show="score=='B'">良好</div>
      <div v-show="score=='C'">及格</div>
      <div v-show="score=='D'">不及格</div>
  
  </div>
  </body>
  <script>
      new Vue({
          el: "#div",
          data: {
              score: 'A'
          }
      });
  </script>
  ```


- 列表渲染：v-for

  ```html
  <body>
  <div id="div">
      <!--v-for：列表渲染，遍历容器的元素或者对象的属性。-->
  
          <!--
            语法1：<标签名 v-for="变量名 in 整数">{{num}}</标签名>
            语法2：<标签名 v-for="变量名 in 数组">{{element}}</标签名>
            语法3：<标签名 v-for="element in 对象数组">{{element}}</标签名>
  				element含义：遍历对象数组中每个对象
            语法4：<标签名 v-for="(element,i) in 对象数组">{{element}}</标签名>
  				element含义：遍历的数组中每个元素对象
  				i含义：循环的索引
          -->
      <h3>v-for循环方式1：固定循环次数</h3>
          <div v-for="num in 6">
              {{num}}
          </div>
  
      <h3>v-for循环方式2：遍历普通数组</h3>
          <div v-for="name in names">
              姓名：{{name}}
          </div>
  
      <h3>v-for循环方式3：遍历对象数组</h3>
  		<div v-for="student in students">
              姓名:{{student.name}},年龄:{{student.age}}
          </div>
  
      <h3>v-for循环方式4：遍历对象数组带索引</h3>
          <div v-for="(student,index) in students">
              序号:{{index+1}},姓名:{{student.name}},年龄:{{student.age}}
          </div>
      
  </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              names:["张三","李四","王五"],
              student:{
                  name:"张三",
                  age:23
              },
              students:[
                  {name:"张三",age:23},
                  {name:"李四",age:25},
                  {name:"王五",age:28}
              ]
          }
      });
  </script>
  ```


- 事件绑定v-on

  ```html
  <body>
      <div id="div">
          <div>{{name}}</div>
  		<!--老方式绑定事件【了解】-->
          <button onclick="demo()">单击_改变div的内容</button>
          <!--
              v-on：为 HTML 标签绑定事件
              完整语法：<标签名 v-on:事件名="vue中methods里面的函数名()"></标签>
              简写语法：<标签名 @事件名="vue中methods里面的函数名()"></标签>
          -->
          <button v-on:click="change()">单击_改变div的内容</button>
          <button v-on:dblclick="change()">双击_改变div的内容</button>
  
          <button @click="change()">简写单击_改变div的内容</button>
      </div>
  </body>
  <script>
      new Vue({
          el:"#div",
          data:{
              name:"黑马程序员"
          },
          methods:{
            change(){
                  if(this.name=="传智播客"){
                      this.name="黑马程序员"
                  }else {
                      this.name = "传智播客"
                  }
              }
          }
      });
  	
  	function demo(){
          alert("demo");
      }
  </script>
  ```


- 绑定属性：v-bind为HTML标签绑定属性值,如href,css等

  ```html
  <body>
      <div id="div">
  	<!--
  		语法1：完整模式
  			  <标签 v-bind:属性名="变量名">
  		语法2：简化模式
  			  <标签 :属性名="变量名">   //v-bind可以省略
  
  		注意：<标签 属性名="{{变量名}}"> 这是不可以的，插入值表达式{{变量名}} 只能放在标签体内
  	-->
          <a href="{{url}}">黑马官网错误展示</a>
  
          <br>
          <!--示例2：使用完整模式（<标签 v-bind:属性名="变量名">）-->
          <a v-bind:href="url">黑马官网完整方式</a>
          <br>
          <!--示例3：使用简化模式（<标签 :属性名="变量名">）-->
          <a :href="url">黑马官网简写方式</a>
          <br>
          <!--示例4：使用简化模式给class属性绑定模型数据cls-->
          <div v-bind:class="cls">我是div</div>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              url:"https://www.itcast.cn",
              cls:"my"
          }
      });
  </script>
  ```


- 表单绑定

  ```html
  <body>
      <div id="div">
          <form autocomplete="off">
  		<!--
  			注意：不用v-model就是单向，使用就是双向
  
  				单向绑定：视图的数据改变了，Mode数据不会被影响
  
                  双向绑定：视图的数据改变了，Mode数据会被影响
  		 -->
              姓名_单向绑定：<input type="text" name="username">
              <br>
              
              姓名_双向绑定：<input type="text" name="username">
  			<br>
  
          </form>
          <hr>
  		<h3>{{username}}</h3>
      </div>
  </body>
  <script src="js/vue.js"></script>
  <script>
      new Vue({
          el:"#div",
          data:{
              username:"张三",
  
          }
      });
  </script>
  ```

  

#### Vue生命周期

- 一共八个阶段，每触发一个生命周期事件，会自动执行一个生命周期方法

  ```html
  new Vue({
  	el:"#app",
  	mounted(){
  	alert("vue挂载完毕，发送异步请求")；
  	}
  });
  ```

- mounted状态：可以数据展现：发送异步请求获取服务器数据返回模型，然后和视图进行绑定

| 状态          | 阶段周期 | 视图和模型状态                       |
| ------------- | -------- | ------------------------------------ |
| beforeCreate  | 创建前   | 视图对象没有，模型对象没有           |
| created       | 创建后   | 视图对象没有，模型对象有             |
| beforeMount   | 载入前   | 视图对象有，模型对象有，没有绑定数据 |
| mounted       | 挂载完成 | 试图对象有，模型对象有，数据绑定     |
| beforeUpdate  | 更新前   | 更新模型数据前，视图显示旧的数据     |
| updated       | 更新后   | 更新模型数据后，视图显示新的数据     |
| beforeDestroy | 销毁前   | 视图和模型对象没有销毁               |
| destroyed     | 销毁后   | 视图和模型对象都被销毁               |