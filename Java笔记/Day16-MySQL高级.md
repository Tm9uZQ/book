#### 约束

- 主键约束

  - 建表时添加主键约束

    ```mysql
    //没有约束名称
    create table 表名 (
    	字段名1 字段类型 primary key,
    	字段名2 字段类型
    );
    
    //有约束名称
    create table 表名 (
    	字段名1 数据类型,
        [constraint 自定义约束名称] primary key(字段名1),
        字段名2 字段类型
    );
    ```

  - 建表后单独添加主键约束

    ```mysql
    alter table 表名 add primary key(字段名);
    ```

  - 删除主键约束

    ```mysql
    alter table 表名 drop primary key;
    ```

  - 主键自增（字段类型必须为数值）

    ```mysql
    字段名 字段类型 primary key atuo_incerment
    ```
    
    ```mysql
    -- 已有主键的字段添加自增
    alter table 表名 change 字段名 字段名 字段类型 auto_incrment;
    ```
    
    

- 唯一约束：`字段名 字段类型 unique`
- 非空约束：`字段名 字段类型 not null`
- 默认约束：`字段名 字段类型 default 值`



- 外键约束

  - 外键：一张表中的某个字段引用其他表的主键，这个字段称为外键

  - 主表：将数据给别人用的表

  - 副表/从表：使用别人数据的表

  - 新建表时增加外键约束

    ```mysql
    create table 表名 (
    	字段名 字段类型,
        字段名 字段类型,
        [constraint 外键约束名] froeign key(外键字段名) references 主表(主键字段名)
    );
    ```

  - 已有表增加外键约束

    ```mysql
    alter table 从表 ADD [constraint 外键约束名称] foreign key(外键字段名) references 主表(主键字段名);
    ```

  - 删除外键约束

    ```mysql
    alter table 表名 drop foreign key 外键约束名;
    ```



#### 数据库设计

- 有哪些表？表里有哪些字段？表和表之间有什么关系？
- 表关系
  - 一对一：多用于表拆分，将一个实体中经常使用的字段放一张表，不经常使用的字段放另一张表，提升查询性能
    - 实现方式：在任意一方加入外键，关联另一方主键，并且设置外键为唯一unique
  - 一对多：一个部门对应多个员工
    - 实现方式：在多的一方建立外键
  - 多对多：一个商品对应多个订单，一个订单包含多个商品
    - 实现方式：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键



#### 事务

- 事务把所有的命令作为一个整体一起向系统提交或撤销操作请求，即这一组命令要么同时成功，要么同时失败

  ```mysql
  -- 开启事务
  start transaction;
  -- 提交事务
  commit;
  -- 回滚事务
  rollback;
  ```

- 事务的四大特性ACID
  - 原子性Atomicity：事务是不可分割的最小操作单位，要么同时成功，要么同时失败
  - 一致性Consistency：使得数据库从一种正确状态转换成另一种正确状态
  - 隔离性Isolation：在事务正确提交之前，不允许把该事务对数据的任何改变提供给任何其他事务
  - 持久性Durability：事务正确提交后，其结果将永久保存在数据库中，即使在事务提交后有了其他故障，事务的处理结果也会得到保存



#### 多表查询

- 表连接查询
  - 内连接
    - 隐式内连接：`select 字段列表 from 表1，表2 where 条件;`
    - 显式内连接：`select 字段列表 from 表1 [inner] join 表2 on 表连接条件;`
  - 外连接
    - 左外连接：`select 字段列表 from 表1 left [outer] join 表2 on 表连接条件;`
    - 右外连接：`select 字段列表 from 表1 right [outer] join 表2 on 表连接条件;`
- 子查询
  - 一个查询语句的结果作为另一个查询语句的一部分
  - 查询结果单行单列，在where后面作为条件
    - `select 查询字段 from 表 where 字段=(子查询);`
  - 查询结果是多行单列，结果类似一个数组，在where后面作为条件
    - `select 查询字段 from 表 where 字段 in (子查询);`
  - 查询结果是多行多列，在from后面作为虚拟表
    - `select 查询字段 from (子查询) 表别名 where 查询条件;`



#### 多表查询

1. 根据需求明确查询哪些表
2. 明确表连接条件去掉笛卡尔积
3. 后续的查询