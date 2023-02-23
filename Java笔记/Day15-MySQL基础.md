#### 数据库

- 数据库：存储和管理数据的仓库，**Database**，简称DB
- MySql服务器中可以创建多个**数据库**，每个数据库可以包含多张**表**，每个表可以存储多条**数据记录**
- MySql是关系型数据库，是由多张相互连接的二维表组成的数据库
- mysql登录：
  - 本机登录`mysql -u用户名 -p`
  - 远程登录`mysql -h远程主机IP -u用户名 -p`



#### SQL

- **DDL**数据定义语言：用来**定义**数据库对象：**数据库、表、列表**
- **DML**数据操作语言：用来对数据库中**表的数据**进行**增删改**
- **DQL**数据查询语言：用来**查询**数据库中**表的数据**
- **DCL**数据控制语言：用来定义数据库的**访问权限**和**安全级别**、**及创建用户**



#### 对比记忆版

- 增
  - 创建数据库：`create database if not exists 数据库名称;`
  - 创建表：`create table 表名 (字段名 数据类型, 字段名 数据类型);`
  - 添加一个字段：`alter table 表名 add 字段名 数据类型;`
  - 给指定列添加数据：`insert into 表名 (字段名1, 字段名2) values (值1, 值2);`
- 删
  - 删除数据库：`drop database if exists 数据库名称;`
  - 删除表：`drop table 表名;`
  - 删除某一字段：`alter table 表名 drop 字段名;`
  - 删除数据：`delete from 表名 [where 条件];`
- 改
  - 修改表/字段：`alter table 表名 ....`（rename to、add、modify、change）
  - 修改数据：`update 表名 set 字段名 = 新的值 [where 条件];`
- 查
  - 查询所有数据库：`show databases;`
  - 查看所有表：`show tables;`
  - 查看表结构：`desc 表名;`



#### DDL

- 操作数据库
  - 查询所有数据库：`show databases;`
  - 创建数据库
    - 普通创建：`create database 数据库名称;`
    - 判断创建：`create database if not exists 数据库名称;`
  - 删除数据库
    - 普通删除：`drop database 数据库名称;`
    - 判断删除：`drop database if exists 数据库名称;`
  - 使用数据库：`use 数据库名称;`

- 操作表
  - 创建表：`create table 表名 (字段名 数据类型, 字段名 数据类型);`
  - 查看所有表：`show tables;`
  - 查看表结构：`desc 表名;`
  - 删除表：`drop table 表名;`
  - 修改表
    - **基础语法**：`alter table 表名 ....`
    - 修改表名：`alter table 表名 rename to 新表名;`
    - 单独添加一个字段：`alter table 表名 add 字段名 数据类型;`
    - 修改某字段的数据类型：`alter table 表名 modify 字段名 新数据类型;`
    - 修改字段名和数据类型：`alter table 表名 change 字段名 新字段名 新数据类型;`
    - 删除某一字段：`alter table 表名 drop 字段名;`



#### DML

- 增加
  - 给指定列添加数据：`insert into 表名 (字段名1, 字段名2) values (值1, 值2);`
  - 给全部列添加数据：`insert into 表名 values (值1, 值2);`
  - 批量添加数据：`insert into 表名 values (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`
- 【修改】：`update 表名 set 字段名 = 新的值 [where 条件];`
- 【删除】：`delete from 表名 [where 条件];`



#### DQL

- 简单查询
  - 普通查询：`select 字段名1, 字段名2 from 表名;`
  - 查询所有字段的简写方法（测试可用，开发效率低不推荐）：`select * from 表名;`
  - 【去重查询】：`select distinct 字段名1 from 表名;`
  - 计算列的值：`select 字段名1(+-*/)字段名2 from 表名;`
  - 起别名：`select 字段名1 as 别名1, 字段名2 as 别名2 from 表名;`
- 条件查询
  - **基础语法**：`select * from 表名 where 条件;`
  - 比较运算：>、<、>=、<=、=、<>或!=不等于
    - `select * from 表名 where 字段名1 > 值1;`
  - 逻辑运算：AND或&&、OR或||、NOT或！
    - `select * from 表名 where 字段名1 > 值1 and 字段名2 < 值2;`
  - 范围查询：
    - 在某个范围内（值1<值2，包头包尾）：`select * from 表名 where 字段名1 between 值1 and 值2;`
    - 多选：`select * from 表名 where 字段名1 in (值1, 值2);`
  - null的处理（is null或is not null）：
    - `select * from 表名 where 字段名1 is null;`
- 模糊查询：`select * from 表名 where 字段名1 like '通配字符串';`
  - %：表示任意多个字符
  - _：表示一个任意字符
- 【排序查询】：`select 字段名 from 表名 order by 列名1 排序方式1, 列名2 排序方式2;`
  - ASC：升序
  - DESC：降序
- 【聚合排序】：`select 聚合函数(字段名) from 表名;`
  - 聚合函数：count、sum、max、min、avg
- 【分组查询】：
  - 分组：`select * from 表名 group by 字段名;`
  - 分组通常和聚合函数一起使用：`select 字段名, 聚合函数(*) from 表名 group by 字段名;`
- 分页查询：`select * from 表名 limit 跳过条数, 显示条数;`



#### 注意点

- 常用数据类型
  - int：整数类型
  - double：小数类型
  - varchar(长度)：可变长度的字符串
  - date：日期类型yyyy-MM-dd
- 添加数据时
  - 字段名和值的数量要对应
  - 值的类型和字段的类型要对应
  - 除了数值类型，其他数据类型的数据都需要加引号
- 聚合函数
  - `select 聚合函数(字段名) from 表名;`记录为null的默认不统计
  - `select 聚合函数(*) from 表名;`可以把所有行数统计进去（包括null）
  - 如果不是数值类型的字段，计算结果为0
- 分组查询固定写法
  - where条件要放在group by的前面
  - where条件不能是聚合函数
  - having条件判断，可以和聚合函数使用，放在group by的后面
- having和where的区别
  - where是在分组前对数据进行过滤，having是在分组后对数据进行过滤
  - where后面不可以使用聚合函数，having后面可以使用聚合函数
- 分页查询limit
  - limit是MySQL的方言
  - Oracle分页查询使用rownumber
  - SQL Server使用top

