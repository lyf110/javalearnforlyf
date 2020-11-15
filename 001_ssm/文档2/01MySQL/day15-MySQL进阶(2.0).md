---
typora-copy-images-to: img
---

# day15-MySQL进阶

# 学习目标

1. 能够使用SQL语句进行排序
2. 能够使用聚合函数
3. 能够使用SQL语句进行分组查询
4. 能够完成数据的备份和恢复
5. 能够说出多表之间的关系及其建表原则
6. 能够理解三大范式 

# 第一章-可视化软件 

## 实操-Navicat的使用

### 1.目标

​	我们上次课是通过命令行方式来操作数据库的, 大家感觉怎么样?  操作起来很不方便, 实际开发中一般使用可视化工具.通过可视化工具可以很方便的来操作数据库.

### 2.步骤

+ 使用可视化工具连接数据库
+ 使用可视化工具对数据库的CRUD
+ 使用可视化工具对表的CRUD
+ 使用可视化工具对数据的CRUD

### 3.讲解

- 连接数据库

  ![1536021958539](img/1536021958539.png)


- 对数据库的操作

  ![img](img/tu_3.gif)


- 对表的操作
- 对数据的操作


### 4.小结

# 第二章-查询记录【重点】

+ 环境准备

```sql
# 创建学生表(有sid,学生姓名,学生性别,学生年龄,分数列,其中sid为主键自动增长)
CREATE TABLE student(
	sid INT PRIMARY KEY auto_increment,
	sname VARCHAR(40),
	sex VARCHAR(10),
	age INT,
  score DOUBLE
);

INSERT INTO student VALUES(null,'zs','男',18,98.5);
INSERT INTO student VALUES(null,'ls','女',18,96.5);
INSERT INTO student VALUES(null,'ww','男',15,50.5);
INSERT INTO student VALUES(null,'zl','女',20,98.5);
INSERT INTO student VALUES(null,'tq','男',18,60.5);
INSERT INTO student VALUES(null,'wb','男',38,98.5);
INSERT INTO student VALUES(null,'小丽','男',18,100);
INSERT INTO student VALUES(null,'小红','女',28,28);
INSERT INTO student VALUES(null,'小强','男',21,95);
```

## 知识点-排序查询

### 1.目标

​	我们天猫/京东里面经常会根据价格来进行排序.  那么在sql里面可以通过排序查询来实现这个功能

### 2.步骤

+ 单例排序
+ 组合排序

### 3.讲解

#### 3.1单列排序

+ 只按某一个字段进行排序，单列排序

```
SELECT 字段名 FROM 表名 [WHERE 条件] ORDER BY 字段名 [ASC|DESC];  //ASC: 升序，默认值; DESC: 降序
```

+ 以分数降序查询所有的学生

```
select * from student order by score desc;
```

#### 3.2组合排序

+ 同时对多个字段进行排序，如果第1个字段相等，则按第2个字段排序，依次类推

```
SELECT 字段名 FROM 表名 WHERE 字段=值 ORDER BY 字段名1 [ASC|DESC], 字段名2 [ASC|DESC];
```

+ 以分数降序查询所有的学生, 如果分数一致,再以age降序

```
select * from student order by score desc, age desc;
```

### 4小结

+ 语法:
+ 应用场景
  - 商城项目里面以价格, 销量排序
  - 社交项目里面以距离排序
  - 一般项目里面有订单的话, 以下单时间 排序



## 知识点-聚合函数   

### 1.目标

​	之前我们做的查询都是横向查询，它们都是根据条件一行一行的进行判断，而使用聚合函数查询是纵向查询，它是对一列的值进行计算，然后返回一个结果值。聚合函数会忽略空值NULL

### 2.步骤

+ max()
+ min()
+ avg()
+ count()
+ sum()

### 3.讲解

#### 3.1语法

| 聚合函数          | 作用          |
| ------------- | ----------- |
| max(列名)       | 求这一列的最大值    |
| min(列名)       | 求这一列的最小值    |
| avg(列名)       | 求这一列的平均值    |
| **count(列名)** | 统计这一列有多少条记录 |
| sum(列名)       | 对这一列求总和     |

- 语法

```sql
SELECT 聚合函数(列名) FROM 表名 [where 条件];
```

#### 3.2练习

```sql
-- 求出学生表里面的最高分数
SELECT MAX(score) FROM student;
-- 求出学生里面最小的age
SELECT MIN(age) FROM student;
-- 求出学生表里面的分数的总和(忽略null值)
SELECT SUM(IFNULL(score,0)) FROM student;
-- 求出学生表里面的平均分
SELECT AVG(score) FROM student;
-- 统计学生的总人数 (忽略null)  
SELECT COUNT(sid) FROM student; 
SELECT COUNT(*) FROM student;
```

- 聚合函数会忽略空值NULL

  ​	我们发现对于NULL的记录不会统计，建议如果统计个数则不要使用有可能为null的列，但如果需要把NULL也统计进去呢？我们可以通过 IFNULL(列名，默认值) 函数来解决这个问题. 如果列不为空，返回这列的值。如果为NULL，则返回默认值。 eg: select sum==(ifnull(chinese,0)==) from student;	            


### 4.小结



## 知识点-分组   

### 1.目标

​	我们可能会遇到这样的常见, 分别统计一个班的男生和女生的数量. 很明显,男生一组,女生一组.  sql中提供了分组来实现这个场景.

### 2.步骤

+ 分组查询概述
+ 分组group的使用
+ 分组后筛选 having的使用

### 3.讲解

#### 3.1概述

​	 分组查询是指使用 GROUP BY语句对查询信息进行分组

​	 GROUP BY怎么分组的？ 将分组字段结果中相同内容作为一组，如按性别将学生分成两组

​	 GROUP BY将分组字段结果中相同内容作为一组，并且返回每组的第一条数据，所以单独分组没什么用处。分组的目的就是为了统计，一般分组会跟聚合函数一起使用

​	1.单独分组没什么用处,每组的第一条数据展示了

​	2.有几组就有几条记录

#### 3.2分组group语法

```
SELECT 字段1,字段2... FROM 表名  [where 条件] GROUP BY 列 [HAVING 条件];
```

+ 练习:根据性别分组, 统计每一组学生的总人数

```
select sex, count(*) from student group by sex;
```

#### 3.4分组后筛选 having

+ 练习根据性别分组, 统计每一组学生的总人数> 5的(分组后筛选)

```
select  sex, count(*) from  student   group by sex having  count(*) > 5
```

+ where和having区别【面试】

![img](img/tu_1.png)

+ 面试题

  ![img](img/tu_2.png)

  Orders表数据如下所示，执行如下SQL语句，运行结果是？

  ```
  select product,sum(price)  from orders group by product where sum(price) > 30;  
  //错误 1.where在group之前 2.where后面不可以使用聚合函数
  ```

### 4.小结



## 知识点-分页

### 1.目标

​	我们在百度搜索的时候,满足条件的数量特别大. 1.为了效率 2.一页展示不下 .我们一般会进行分页查询. MySql里面提供了分页的语法(注意这个是方言)

 

 ![1553673609295](img/1553673609295.png)

 ![1553673621063](img/1553673621063.png)

### 2.步骤

+ limit的使用

### 3.讲解

#### 3.1概述

​	LIMIT是限制的意思，所以LIMIT的作用就是限制查询记录的条数. 经常用来做分页查询

#### 3.2 语法

```
select ... from .... limit a ,b.
```

![img](img/tu_3.png)



+ b: 一页显示的数量(我们根据需求自己定义的)  
+ a: 从哪里开始查询(从0开始计数) ;  

```sql
-- 分页查询学生, 一页显示3条  b = 3; 
-- a = (当前页码-1)*b;
-- 第一页  a =0, b = 3 
SELECT * from student limit 0,3;
-- 第二页 a = 3 ,b = 3;
SELECT * from student limit 3,3;
-- 第三页 a = 6 ,b = 3;
SELECT * from student limit 6,3;
```

## 知识点-查询的语法总结

### 1.目标

查询语法是特别多的, 下面我们就把查询的语法的总结一下

### 2.步骤

+ 书写的顺序
+ 执行的顺序

### 3.讲解

+ 书写顺序

```
select ... from...where...group...having...order...limit
```

+ 执行顺序

```
from...where...group....having...select...order...limit
```

### 4.小结

# 第三章-多表间的关系【重点】

## 知识点-为什么要拆表

### 1. 目标

​	有些情况下,使用一张表表示数据 数据不好维护, 存在数据冗余现象

### 2.步骤

+ 准备两张表
+ 进行分析

### 3.讲解

#### 3.1准备工作

创建一张分类表(类别id,类别名称.备注:类别id为主键并且自动增长)

创建一张商品表(商品id,商品名称,商品价格,商品数量,类别.备注:商品id为主键并且自动增长)

```
-- 创建一张分类表(类别id,类别名称.备注:类别id为主键并且自动增长)
CREATE TABLE t_category(
	cid INT PRIMARY KEY auto_increment,
	cname varchar(20)
);

INSERT INTO t_category values(null,'手机数码');
INSERT INTO t_category values(null,'食物');


-- 创建一张商品表(商品id,商品名称,商品价格,商品数量,类别编号.备注:商品id为主键并且自动增长)
CREATE TABLE t_product(
	pid INT PRIMARY KEY auto_increment,
	pname varchar(40),
	price DOUBLE,
	num INT,
	cno INT
);

INSERT INTO t_product values(null,'Mac',18000,10,1);
INSERT INTO t_product values(null,'iPhonex',6000,100,1);
INSERT INTO t_product values(null,'iPhone6s',5000,10,1);
INSERT INTO t_product values(null,'iPhone5s',4000,10,1);
INSERT INTO t_product values(null,'iPhone5',3000,20,1);
INSERT INTO t_product values(null,'iPhone4s',2000,20,1);

INSERT INTO t_product values(null,'方便面',4.5,1000,2);
INSERT INTO t_product values(null,'矿泉水',3.5,100,2);
INSERT INTO t_product values(null,'咖啡',10,3000,2);


```

#### 3.2分析

 ![1553673959962](img/1553673959962.png)

### 4.小结



## 知识点-引用完整性【重点】

### 1.目标

​	表和表之间存在一种关系，但是这个关系需要外键约束来维护

### 2.步骤

+ 外键约束的语法
+ 外键约束的使用
+ 外键的级联操作

### 3.讲解

#### 3.1外键的使用

![1536027478187](img/1536027478187.png)

+ 添加外键语法

```
alter table 表 add [CONSTRAINT] [外键名称] foreign key(字段) references 表（字段);

alter table 表 add  foreign key(列) references 表（列);
-- 给商品表添加外键
alter table t_product add foreign key(cno) references t_category(cid);
```

+ 删除外键语法

```
ALTER TABLE 表 drop foreign key 外键名称;
```

![1536913612401](img/1536913612401.png)

**注意:**

​	外键列的类型一定要和参照列(主键)的类型一致

​	有主键才能有外键, 参照的列(主键)必须为主键  

+ 外键练习

```
//学生表
CREATE TABLE student(
	sid int primary key,
	name varchar(50) not null,
	sex varchar(10)
);
	
//分数表
create table score( 
	id int primary key,
	score int,
	sid1 varchar(20) 
);



添加外键方式:
	ALTER TABLE score ADD  FOREIGN KEY(sid1) REFERENCES student(sid);  //错误的, sid1是字符串类型, 引用的sid是int类型
```

#### 3.2外键的级联  

删除一方的时候另外的一方的数据没有任何用途的时候才可以使用 eg: 公司和员工

+ 要把类别的id值1，改成5，能不能直接修改呢？

```sql
UPDATE categoey SET cid=5 WHERE cid=2;
```

​	不能直接修改:Cannot delete or update a parent row: a foreign key constraint fails 如果副表(商品表)中有引用的数据,不能直接修改主表(类别表)主键

+ 要删除类别为1的类别,能不能直接删除呢？

```sql
DELETE FROM categoey WHERE id = 1;
```

​	不能直接删除:Cannot delete or update a parent row: a foreign key constraint fails 如果副表(商品表)中有引用的数据,不能直接删除主表(类别表)数据

+ 什么是级联操作

  在修改和删除主表的主键时，同时更新或删除副表的外键值，称为级联操作
  `ON UPDATE CASCADE` -- 级联更新，主键发生更新时，外键也会更新
  `ON DELETE CASCADE` -- 级联删除，主键发生删除时，外键也会删除

具体操作：

- 删除product表
- 重新创建product表，添加级联更新和级联删除

```sql
ALTER TABLE product ADD FOREIGN KEY(cno) REFERENCES category(cid) ON UPDATE CASCADE ON DELETE CASCADE
```

### 4.小结



## 知识点-多表间关系一对多

### 1.目标

​	我们生活里面有很多一对多的例子. eg: 类别和商品, 用户和订单.  一对多的关系,可以通过外键维护. 我们重点关注的是怎么创建外键

### 2.步骤

+ 通过类别和商品的例子来讲解一对多

### 3.讲解

- 在多方创建一个字段作为外键，指向一方主键

![1536033119270](img/1536033119270.png)

```
alter table product add foreign key(cid) references  category(cid)
```

### 4.小结



## 知识点-多表间关系多对多【重点】      

### 1.目标

​	我们生活里面有很多对多的例子. eg: 学生和课程, 老师和学生.  多对多的关系,可以通过外键维护. 我们重点关注的是怎么创建外键

### 2.步骤

- 通过学生和课程的例子来讲解多对多

### 3.讲解

- 新建一张中间表，**至少**包含两个字段，都作为外键，分别指向各自的主键

![img](img/tu_5.png)



```
alter table s_c_table add foreign key(sno) references student(sid);
alter table s_c_table add foreign key(cno) references cource(cid);
```

### 4.小结

## 知识点-一对一【了解】

### 1.目标

​	1对1的话在一张表里面就可以表达. 如果要拆表也可以. eg:把用户信息进行拆表.  为了提升用户体验, 用户注册的时候只需要输入用户名,手机号码, 密码. 等用户注册成功了再进行个人信息的完善. 

### 2.步骤

+ 通过一个例子进行讲解

### 3.讲解

- 先当做一对多，在外键字段添加唯一约束。


![1536918346299](img/1536918346299.png)

### 4.小结

## 扩展-旅游项目里面表关系分析

### 1.需求

+ 分析出旅游项目里面表关系

### 2.分析

### 3.实现



![1536919439600](img/1536919439600.png)

### 4.小结

# 第四章-数据的备份和还原  【会用】

## 知识点-数据的备份和还原

### 1.目标

​	在服务器进行数据传输、数据存储和数据交换，就有可能产生数据故障。比如发生意外停机或存储介质损坏。这时，如果没有采取数据备份和数据恢复手段与措施，就会导致数据的丢失，造成的损失是无法弥补与估量的。

### 2.步骤

+ 使用命令行
+ 使用可视化工具

### 3.实现

### 4.小结

# 第五章-数据库设计三大范式    

## 知识点-数据库设计三大范式 

### 1.目标 

​	好的数据库设计对数据的存储性能和后期的程序开发，都会产生重要的影响。建立科学的，规范的数据库就需要满足一些规则来优化数据的设计和存储，这些规则就称为范式。 

### 2.步骤

+ 1NF
+ 2NF
+ 3NF

### 3.讲解

#### 3.1NF

##### 3.1.1概述

​	数据库表的每一列都是不可分割的原子数据项，不能是集合、数组等非原子数据项。即表中的某个列有多个值时，必须拆分为不同的列。简而言之，第一范式每一列不可再拆分，称为原子性

##### 3.1.2 应用

![img](img/tu_11.png)

##### 3.1.3 小结

​	如果不遵守第一范式，查询出数据还需要进一步处理（查询不方便）。遵守第一范式，需要什么字段的数据就查询什么数据（方便查询）

#### 3.2 2NF

##### 3.2.1概述

​	在满足第一范式的前提下，表中的每一个字段都完全依赖于主键。所谓完全依赖是指不能存在仅依赖主键一部分的列。简而言之，第二范式就是在第一范式的基础上所有列完全依赖于主键列。当存在一个复合主键包含多个主键列的时候，才会发生不符合第二范式的情况。比如有一个主键有两个列，不能存在这样的属性，它只依赖于其中一个列，这就是不符合第二范式。

简而言之,第二范式需要满足:

1. 一张表只描述一件事情
2. 表中的每一个列都依赖于主键

##### 3.2.2 应用

![img](img/tu_13.png)

##### 3.3.3小结

​	如果不准守第二范式，数据冗余，相同数据无法区分。遵守第二范式减少数据冗余，通过主键区分相同数据。

#### 3.3  3NF

##### 3.3.1概述

​	在满足第二范式的前提下，表中的每一列都直接依赖于主键，而不是通过其它的列来间接依赖于主键。简而言之，第三范式就是所有列不依赖于其它非主键列，也就是在满足2NF的基础上，任何非主列不得传递依赖于主键。所谓传递依赖，指的是如果存在"A → B → C"的决定关系，则C传递依赖于A。因此，满足第三范式的数据库表应该不存在如下依赖关系：主键列 → 非主键列x → 非主键列y

##### 3.3.2应用

![img](img/tu_14.png)

##### 3.3.5小结

​	如果不准守第三范式，可能会有相同数据无法区分，修改数据的时候多张表都需要修改（不方便修改）。遵守第三范式通过id可以区分相同数据，修改数据的时候只需要修改一张表（方便修改）。

### 4.小结

![img](img/tu_15.png)




