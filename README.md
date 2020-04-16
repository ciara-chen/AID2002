# Data

## 文件处理

## 正则表达式



- `re.findall('(\w+)\s+(.*)','xxx   xxxxx x x x x')`括号里的内容是需要得出的结果，\s+不会被打印出来





## 数据库

- 数据库：按照数据**一定结构**，存储管理数据的仓库
- 数据库管理系统与数据库系统

- 数据库分类：关系型数据库（二维表）与非关系型数据库



### 1.   MySQL: Structured Query Language

---

- 连接数据库

  ```mysql
  mysql -u root -p;
  ```

  > 连接数据库模板： mysql -h 主机地址 -u 用户名 -p
  >
  > SQL每条命令以;结尾
  >
  > 除命名外，SQL命令不区分大小写

- 关闭连接

  ```mysql
  ctrl-D
  exit
  ```

---



### 2.   数据库管理

---

#### 		2.1.   增



#### 		2.2.   查



#### 		2.3.   改



#### 		2.4.   删



### 3.   数据表管理

---

#### 		3.1.   表头（字段）

##### 					3.1.1.   增



##### 					3.1.2.   查



##### 					3.1.3.   改



##### 					3.1.4.   删



#### 		3.2.   表身（数据内容）

##### 					3.2.1.   增



##### 					3.2.2.   查



##### 					3.2.3.   改



##### 					3.2.4.   删

---



### 4.   索引操作

---

- 概念：由需要查询的主表中的一列(多列)组成的只有查询功能的表；通过查询索引表中的数据，提取主表中对应的整行(部分)数据内容。

  > 索引类似于生成了一个字典的键，而主表中对应的数据是其值。所以它的优点也是快速查询，缺点是占用空间
  >
  > 索引一般创建在经常需要查找的字段上，且数据量庞大时才能体现其优势；由于索引只有查询功能，如果需要频繁书写，它也并不占优势

- 分类：

  - 普通索引(MUL)：字段值无约束
  
  - 唯一索引(UNI)：字段值不可重复，可为NULL
  
  - 主键索引(PRI)：字段值不可重复，也不可为NULL
  
    

####        4.1.   创建索引

- 创建表时创建索引

  ```mysql
  create table 表名(
  字段名 数据类型，
  字段名 数据类型，
  index 索引名(字段名),
  index 索引名(字段名),
  unique 索引名(字段名)
  );
  >>  create table moments (
  id int primary key,
  cotent test,
  time datetime,
  index content_index(content),
  unique datetime_index(datetime)
  );
  ```

- 对已存在表格创建索引

  ```mysql
  create [unique] index 索引名 on 表名(字段名);
  >>  create unique index name_index on cls(name);
  ```

- 对已存在的表格创建主键索引

  ```mysql
  alter table 表名 add primary key (字段名)
  >>  alter table class add primary key (id)
  ```

---



####       4.2.   查询索引

```mysql
1、desc cls;                           --> KEY标志为：MUL 、UNI。
2、show index from cls;
```



####        4.3.   删除索引

```mysql
1、drop index score_index on cls;
2、alter table cls drop primary key;      #删除主键
```



####       4.4.   复合键

```mysql
primary key (uid,pid)
```

> 这是两个表的多对多的关系中生成的第三张表的一一配对且不重复的列表结果。

---



### 5.   外键约束

---

#### 	5.1.   概念

​		需要兼并两份有关联的表的(部分)数据，为了避免数据的冗余，使用外键将两份表连接上；之所以是约束，是因为关联它们的同时对它们产生一定的限制。

> 由于外键列表是关联主表中的某一列数据，所以外键列表的数据类型必须与主表中的关联列表数据类型相同



#### 	5.2.   图解

​		 好比一份部门信息表和一份员工信息表。员工都有所属部门，如果将两份表格合并，那么员工信息表中就得再次部门信息，当多个员工所属同个部门时，就会重复部门信息数据，这样就产生了信息的冗余。此时加一个外键用一个id来代表部门的对应信息，就避免了数据的重复记录。

![image-20200331161906201](/home/tarena/.config/Typora/typora-user-images/image-20200331161906201.png)



#### 	5.3.   代码：

##### 		5.3.1.    建外键约束

```mysql
# 创建表时直接建立外键
create table `person` (
	`id` int primary key auto_increment,
    ...
    `dept_id` int default null,
    constraint dept_fk foreign key (dept_id) references dept(id) [on delete restrict on update restrict]);
```

```mysql
#  建立表后增加外键
alter table person add constraint dept_fk foreign key (dept_id) references dept(id) [on delete restrict on update restrict];
```

> 在代码中添加``是避免与mysql中的关键字冲突，意为还原其本身含义。
>
> constraint 外键名称：可不加
>
> []中的信息是级联动作，可以理解为建立外键所带来的约束，详情查看5.3.3
>
> 建立外键约束的同时，外键列表会自动产生对应列的普通索引，索引名=外键名



##### 		5.3.2.   删除外键约束

```mysql
alter table person drop foreign key dept_fk;
drop index dept_fk on person;
```

> 外键约束无法查看，只能通过`desc 表名`来查看字段`key`中是否有`MUL`提示，无法查看外键的约束类型。由于外键的产生会生成索引，所以删除外键时也需要把对应的索引也删除才能算彻底删除外键。
>



##### 		5.3.3.   级联动作：

- restrict(默认)：被从表使用了的主表相关联记录不可修改跟删除。
- cascade：主表删除或修改相关联字段时，从表会实时更新。
- set null：当主表删除或修改时，从表相关联数据都会显示null。



##### 		5.3.4.   练习：

- month2/day08/mysql_4.sql -->朋友圈设计
- month2/day09/mysql_5.sql -->作业讲解.1

---



### 6.   表关联关系

---

#### 	6.1.   数据关系

- 一对一关系：一张表的一条记录一定只能与另外一张表的一条记录进行对应，反之亦然。

  > 举例 : 学生信息和学籍档案，一个学生对应一个档案，一个档案也只属于一个学生
  >
  > 当两个表的数据关系是一对一且创建外键约束时，可以使用cascade的级联动作起到实时更新。

- 一对多关系：一张表中有一条记录可以对应另外一张表中的多条记录；但是反过来，另外一张表的一条记录只能对应第一张表的一条记录，这种关系就是一对多或多对一

  > 举例： 一个人可以拥有多辆汽车，每辆车登记的车主只有一人。

- 多对多关系：一对表中（A）的一条记录能够对应另外一张表（B）中的多条记录；同时B表中的一条记录也能对应A表中的多条记录

  > 举例：一个运动员可以报多个项目，每个项目也会有多个运动员参加,这时为了表达多对多关系需要单独创建关系表。
  >
  > 当表格之间数据关系非一对一的情况，那么创建外键约束时，可以用默认的restrict来避免数据的误操作



#### 	6.2.    E-R模型

- 定义：E-R模型(Entry-Relationship)即 实体-关系 数据模型,用E-R图来直观反映实体、属性与关系之间的联系

- ER图的绘制：矩形框代表实体,菱形框代表关系,椭圆形代表属性

  ![er](/home/tarena/CYM/month2/notes/imag/er.PNG)



#### 	6.3.   关联表整理

##### 		6.3.1.   表关联查询

```mysql
select * from dept,person where dept.id = person.dept_id;
```



##### 		6.3.2.   内连接

> 与表关联查询一样，官方更推荐使用内连接查询

```mysql
select * from person inner join  dept  on  person.dept_id =dept.id;
```



##### 		6.3.3.   左连接

> 左表为主表，显示右表中与左表匹配的项

```mysql
select * from person left join  dept  on  person.dept_id =dept.id;
```



##### 		6.3.4.   右连接

> 右表为主表，显示左表中与右表匹配的项

```mysql
select * from person right join  dept  on  person.dept_id =dept.id;
```

> 注意：我们尽量使用数据量大的表作为基准表，即左连接做左表，右连接做右表



##### 		6.3.5.   练习：

- month2/day08/mysql_4.sql -->多表查询
- month2/day09/mysql_5.sql -->作业讲解.3

---



### 7.   视图

---

#### 	7.1.   概念

- 定义：是将一份原大数据表进行了进一步的分类管理，可以便捷用户每次查询时，数据库都要重新反复浏览原表一遍。而视图是一个或多个原表根据数据库查询语句导出来的一个镜像表，对视图增改删可以直接影响原表。

  > 视图是虚拟表，本身不存储数据，而是按照指定的方式进行查询。

- 举例描述视图概念：朕想要了解皇宫的国库的相关情况，想知道酒窖有什么酒，剩多少，窖藏多少年，于是派最信任的高公公去清点，高公公去国库清点后报给了朕；朕又想知道藏书情况，于是又派高公公去清点并回来报告给朕，又想知道金银珠宝如何，又派高公公清点。。。过一段时间又想知道藏书情况，高公公还得重新再去清点，皇上问一次，高公公就得跑一次路。

  后来皇上觉得高公公不容易，就成立了国库管理部门，小邓子负责酒窖，小卓子负责藏书，而小六子负责金库的清点。。。后来皇上每次想了解国库就直接问话负责人，负责人就按照职责要求进行汇报。 

  ![](/home/tarena/CYM/month2/notes/imag/视图举例.png)

  ​		安排专人管理后，每次皇上想要了解国库情况，就不必让高公公每次都跑一趟，而是指定的人员按照指定的任务完成指定的汇报工作就可以了。

  ​		和数据库相对应，每次进行查询工作，都需要编写查询代码进行查询；而视图的作用就是不必每次都重新编写查询的SQL代码，而是通过视图直接查询即可。

---



#### 	7.2.   创建视图

```mysql
create or replace view c1 as select name,age from cls;
```

> 同一个库中，视图名称不可重复；
>
> `or replace`可选，意为当有相同视图名称时，替换那份视图表；
>
> 创建视图只是比查询语句多了`create or replace view...as....`



#### 	7.3.   查看现有的视图

```mysql
show full tables in stu where table_type like 'VIEW';
```

> `show tables`结果中也能查询到视图
>
> 由于视图也是一个数据表，只是一个虚拟的表，所以它也能适用所有表数据的操作，增删改查都能在视图表中实现。



#### 	7.4.   查看视图表详情

```mysql
1、desc c1;
2、show fields from c1;
```



#### 	7.5.   修改视图（字段）

```mysql
alte view c1 as select name,age,score from cls;
```

> 修改视图字段有点像替换的过程，只是语言不同，一个是`replace`，一个是`alter`，但是他们都将重新查询并覆盖到原来的view中。

---



#### 	7.6.   修改视图表数据

- 视图表跟正常表的数据修改一样的操作，`update, intsert, delete,select`都通用于视图表，只是修改视图表的同时，数据一样会传送的原表数据，所以会一并更新至原表。

  > 1、由于视图是个镜像表，所以当原表修改时，视图也会修改。所以视图与原表的修改是会互相影响的。而当原表改名或删除时，对应视图将同时失效。
  >
  > 2、视图可以是多表数据合并导出的，但是它在修改时，一次只能对对应的一个原表数据进行修改，同时修改两个，数据库会报错。

- 拓展部分：视图修改和录入约束`with check option`

  ​		 由于视图表是原表满足某种条件后生成的一个表，而当你插入或修改视图表时也会影响原表。如果你插入或修改的数据不满足该视图表从原表导出的条件时，你再次查看视图表的数据会觉得无故消失了你录入的数据。其实并没有，只是录入的数据不满足生成的视图表的条件，所以视图表中不显示，但是数据已存入原表。如果希望你录入的数据能出现在视图表中，那么你也必须录入符合该视图表条件的数据，用`with check option`可以用来检测你录入的数据是否符合视图表显示的要求，如果不满足，则无法执行。

  ![](/home/tarena/CYM/month2/notes/imag/with_check_option.png)

  > 为了通过视图能更直观查看增删改的情况，同时保障对原表的安全操作，没有特殊的理由，建议加上“WITH CHECK OPTION”命令。

---



#### 	7.7.   删除视图

```mysql
drop view if exists c1;
```

> `if exists`是保障该视图表存在则删除，不存在也不报错。



#### 	7.8.   练习：

- month2/day08/mysql_4.sql -->视图操作

  

#### 	7.9.   视图优缺点：

- 作用
  1. 是对数据的一种重构，不影响原数据表的使用。
  2. 简化高频复杂操作的过程，就像一种对复杂操作的<font color=red>封装</font>。
  3. 提高安全性，可以给不同用户提供不同的视图。
  4. 让数据更加清晰。

- 缺点
  1. 视图的性能相对较差，从数据库视图查询数据可能会很慢。

---



### 8.   函数和存储过程

- 事先经过编译并存储在数据库中的一段sql语句集合，调用存储过程和函数可以简化应用开发工作，提高数据处理的效率。

  

#### 	8.1.   函数创建

```mysql
#无参数的函数调用
delimiter $$                  # 更改mysql结束语标识
create function st() returns int                  #注意此处是returns，后面是返回值的数据类型
begin 
return (select score from class_1 order by score desc limit 1);       #除了函数内的代码需要“;”外，其他行不需要
end $$                     #由于上面的结束语标识更改成$$,所以此处结束是也要伴随$$
delimiter ;                  #将mysql结束语标识更改回来

select st();           #调用函数使用select
```

```mysql
# 含有参数的函数调用
delimiter $$
create function queryNameById(uid int(10))         #()里的是形参列表，需要包含参数名，参数类型，10不代表什么只是希望数字宽度不超过10，但是不限制
returns varchar(20)
begin
return  (select name from class_1 where id=uid);
end $$
delimiter ;

select queryNameById(1);
```

> 1、由于函数体内会有多行语句，为避免数据库系统误以为创建函数已结束，需要执行，先将mysql的结束语标识更改，当函数体创建真正结束时，需要将其再更改回来，这样在执行函数体时才能分句执行。
>
> 2、数据库函数返回只能是<font color=red>一个明确值</font>，不可以是列表元组或多条数据等，也不能有查询操作。
>
> 3、函数名在一个库中不可重复

---



#### 	8.2.   存储过程

##### 		8.2.1.    创建存储过程

```mysql
#  存储过程创建和调用
delimiter $$
create procedure st()                           #注意无returns
begin 
    select name,age from class_1; 
    select name,score from class_1 order by score desc; 
end $$
delimiter ;

call st();             #调用使用call
```



##### 		8.2.2.   形参使用类型

```mysql
#  分别将参数类型改为IN OUT INOUT 看一下结果区别
delimiter $$
create procedure p_out ( OUT num int )    # ()里的是形参列表，在此之前还要设置参数执行类型3种
begin
    select num;
    set num=100;
    select num;
end $$

delimiter ;

set @num=10;          #用户变量
call p_out(@num)          

#结果显示
>>out类型：null,  100,  此时的@num=100
>>in类型：10,   100，此时的@num=10
>>inout类型：10,   100,  此时的@num=100
```

- IN 类型：参数可以接收变量也可以接收常量，传入的参数在存储过程内部使用即可，但是在存储过程内部的修改无法传递到外部。
- OUT 类型：参数只能接收一个变量，接收的变量不能够在存储过程内部使用（内部为NULL），但是可以在存储过程内对这个变量进行修改。因为定义的变量是全局的，所以外部可以获取这个修改后的值。
- INOUT类型：参数同样只能接收一个变量，但是这个变量可以在存储过程内部使用。在存储过程内部的修改也会传递到外部。

---



#### 	8.3.   变量设置与使用

- 局部变量定义`declare`：只能在函数体中

  ```mysql
  create function st1() returns int
  begin 
  declare val1 float;
  declare val2 float;
  set val1=select score from cls order by score desc limit 1;
  select score from cls order by score limit 1 into val2;
  return val1+val2;
  end $$
  ```

  > 1、定义变量的同时需要设置数据类型
  >
  > 2、通用变量赋值方式有两种：a. set 变量=xxx， b. xxx into 变量

- 用户变量：`set @`

  ```mysql
  set @a=10;
  call p_out(@a);
  ```

  > 使用用户变量时必须跟随`@`,证明他是个用户变量

---



#### 	8.4.   存储过程和函数操作

##### 		8.4.1.   调用

```mysql
#存储过程
call p_out;
#函数
select st();
```

​		

##### 		8.4.2.   查询函数与存储过程的信息

```mysql
show procedure / function status like "st";
```

> like:模糊查找的方式



##### 		8.4.3.  查看存储过程和函数的源代码

```mysql
show create procedure / function st;
```



##### 		8.4.4.   查看某个数据库内的函数和存储过程

```mysql
select name from mysql.proc where db='stu' and type='procedure / function';
```



##### 		8.4.5.   删除函数或存储过程

```mysql
drop function / procedure if exists queryNameById;
```

---



#### 	8.5. 练习：

- month2/day08/mysql_4.sql -->函数使用

- month2/day09/mysql_5.sql -->作业讲解.2

  

#### 	8.6.   区别：

1. 函数有且只有一个返回值，而存储过程不能有返回值。
2. 函数只能有输入参数，而存储过程可以有in,out,inout多个类型参数。
3. 存储过程中的语句功能更丰富，实现更复杂的业务逻辑，可以理解为一个按照预定步骤调用的执行过程，而函数中不能展示查询结果集语句，只是完成查询的工作后返回一个结果，功能针对性比较强。
4. 存储过程一般是作为一个独立的部分来执行(call调用)。而函数可以作为查询语句的一个部分来调用。

---



### 9.   事务控制

#### 	9.1.   概念

- 事务主要用于处理操作量大，复杂度高的数据；它是一个连续的一组数据库操作。
- 不是所有SQL服务器都支持事务，mysql支持。
- 由于事务是一组连续的数据库操作，所以它必须同时操作成功，如果有一个不成功则所有数据都不动。这样的数据库操作语句就构成了一个事务，事务主要处理数据的<font color=red>增删改</font>操作。
- 事务确保了操作过程中的安全。



#### 	9.2.   事务操作

```mysql
#  事务中SQL命令都执行成功,提交到数据库,结束!
begin;          # 开启一个事务
select * from cls;
delete from cls where sex is null;
update cls set age=age+1;
select * from cls;
commit;       #执行以上操作
```

```mysql
#  有SQL命令执行失败或不恰当,回滚到初始状态,结束!
begin;
select * from cls;
update cls set age=age+1;
delete from cls where id > 3;
select * from cls;
rollback;       #不执行以上操作
```

> 只要未提交`commit`，那是事务都将不执行。



#### 	9.3.   四大特性

- 原子性：事务的数据库操作，成功则所有语句成功，只要一个操作不成功，则所有操作都不成功。
- 一致性：事务里的所有操作处于一致状态，由于原子性>>一致性。
- 隔离性：允许多个事务并发，且互相独立。隔离行可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。
- 持久性：事务一旦提交，其修改永久保存在数据库中。即使数据库突然退出或崩溃，修改的数据也依然被保存在数据库里。



#### 	9.4.   隔离级别

​		不同数据库的隔离级别不同，那么隔离级别不同带来的操作现象也有区别：

1. 读未提交：read uncommitted
   - 并发的事务A与事务B，当事务A未提交(commit)数据时，事务B可以读到事务A操作。
   - 此时事务A的数据是不确定的，可能会rollback，所以此时的数据是“脏数据”。
   - 级别最低，一般数据库隔离级别都高于此。
2. 读已提交：read committed
   - 并发的事务A与事务B，当事务A提交(commit)数据时，事务B可以读到事务A操作。
   - 解决了“脏数据”。
   - 不可重复读，即事务A提交后，事务B没有任何操作，数据却变化了。
3. 可重复读：repeated read
   - 并发的事务A与事务B，当事务A提交(commit)数据时，事务B仍然看不到事务A的操作。
   - 存在“幻读”：当并发事务对同一行数据采取增改删时，会出现不同现象的幻读。a.当事务A提交，事务B对同一行的不同数据进行修改时，结果会发现事务A对该行的操作也显示出来了，即事务B未对相关数据操作，数据却自行更改了。b. 当事务A删除了某行数据并提交，事务B仍对该行进行了修改，显示操作成功，但是查询时却发现数据无变化。c.当事务A对某行进行了增改删操作却未提交，事务B再进行操作时系统会等待事务A对此的提交，或超过等待时间直接报错。
   - mysql的隔离级别。
4. 串行化：serializable
   - 不能存在并发事务。
   - 数据库很少使用，因为吞吐量太低，用户体验差。

---



### 10.   数据库优化

#### 	10.1.   数据库设计范式

- 共6中范式（行为规范），目的是降低数据冗余。
- 第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。
- 各种范式呈递次规范，越高的范式数据库冗余越小。但是范式越高也意味着表的划分更细，一个数据库中需要的表也就越多，那么就会存在更多的外键，这严重地降低了系统运行性能。所以通常数据库设计遵循第一第二第三范式，以避免数据操作异常,又不至于表关系过于复杂。



#### 	10.2.   MYSQL存储引擎

- 定义：mysql数据库管理系统中用来处理表的处理器

- MySQL由于开源，拥有多个存储引擎

- MYSQL默认使用InnoDB:支持行级锁

- 通过设置合理的引擎，我们可以对数据库起到优化的作用

- 基本操作：

  ```mysql
  1、查看所有存储引擎
     mysql> show engines;
  2、查看已有表的存储引擎
     mysql> show create table 表名;
  3、创建表指定
     create table 表名(...)engine=MyISAM,charset=utf8,auto_increment=10000;
  4、已有表指定
     alter table 表名 engine=InnoDB;         #修改引擎非常耗时，特别是数据特别庞大时
  ```



#### 	10.3.   字段数据类型选择

- 优先程度 数字 > 时间日期 > 字符串

- 同一级别 占用空间小的 > 占用空间多的

  > 字符串在查询比较排序时数据处理慢
  > 占用空间少，数据库占磁盘页少，IO处理就更快

- 对数据存储精确不要求 float > decimel
- 如果很少被查询可以用 TIMESTAMP（时间戳实际是整形存储）



#### 	10.4.   键的设置

- Innodb如果不设置主键也会自己设置隐含的主键，所以最好自己设置
- 尽量设置占用空间小的字段为主键
- 外键的设置用于保持数据完整性，但是会降低数据导入和操作效率，特别是高并发情况下，而且会增加维护成本
- 虽然高并发下不建议使用外键约束，但是在表关联时建议在关联键上建立索引，以提高查找速度



#### 	10.5.   explain语句

```mysql
explain select * from cls;
```



#### 	10.6.   SQL优化

- 主要查看老师的notes跟视频第二阶段-concurrent-day05



#### 	10.7.   表的拆分

- 垂直拆分
- 水平拆分：

---



### 11.   数据库备份和用户管理

#### 	11.1.   复制表

- 不同于视图，是复制一份表，与原表独立

  ```mysql
  create table good_student select * from cls where score > 80;
  ```

  > 复制的表无主键，需要额外设置



#### 	11.2.   数据库备份

- 此命令不是sql语句，在终端命令行中执行

  ```
  mysqldump -u root -p stu > ./stu.sql     #注意备份时是符号">""
  ```

- 恢复数据库

  ```
  mysql -u root -p student < ./stu.sql       #注意恢复时是符号"<""
  ```



#### 	11.3.   用户权限管理

- 开启MySQL远程连接

  ```
  更改配置文件，重启服务！
  1.cd /etc/mysql/mysql.conf.d
  2.sudo vi mysqld.cnf  找到44行左右,加 # 注释
     # bind-address = 127.0.0.1
     
  3.保存退出
  4.sudo service mysql restart
  5.进入mysql修改用户表host值 
    use mysql;
    update user set host='%' where user='root';         #%代表任意的IP地址
  6.刷新权限
    flush privileges;        #重启数据库权限
  ```

- 添加授权用户 

  ```
  1. 用root用户登录mysql
     mysql -u root -p
  2. 添加用户 % 表示自动选择可用IP
     CREATE USER 'vip'@'%' IDENTIFIED BY 'password';       #%代表可以任意的IP地址去登入
  3. 授权
     grant select,insert on stu.cls to "vip"@"%" [identified by "密码"] with grant option;        #授权,密码可写可不写，写了就是更改登入密码
     revoke insert on stu.cls from 'vip'@'%';           #删除权限
  4. 刷新权限
     flush privileges;
  5. 删除用户
     drop user "vip"@"%"
  ```

---



### 12.   pymysql模块

- 使用Python来操作模块，ptmysql是第三方库
- pymysql使用流程
  1. 建立数据库连接(db = pymysql.connect(...))
  2. 创建游标对象(cur = db.cursor())
  3. 游标方法: cur.execute("insert ....")
  4. 提交到数据库或者获取数据 : db.commit()/db.fetchall()
  5. 关闭游标对象 ：cur.close()
  6. 断开数据库连接 ：db.close()



#### 	12.1.   pymysql基本流程

```python
# 连接数据库
db = pymysql.connect(host='localhost',
                     port=3306,
                     user='root',
                     password='123456',
                     charset='utf8')

# 创建游标（调用sql语句，获取执行结果）
cur = db.cursor()

# 各种数据操作

# 关闭游标和数据库
cur.close()
db.close()
```



#### 	12.2.   常用函数

##### 		12.2.1.   读语句示例

```python
#各种数据操作部分--> 查询语句
sql = 'select name, age,sex,score from cls where score > 85'
cur.execute(sql)

# 第一种
for i in cur:
    print(i)  # 一条查询记录

# 第二种
one = cur.fetchone()  # 获取一个查询记录
print(one)

many = cur.fetchmany(2)  # 获取多个查询记录
print(many)

all = cur.fetchall()  # 获取所有查询记录
print(all)

```

> cur.fetchone() 获取查询结果集的第一条数据，查找到返回一个元组否则返回None
> cur.fetchmany(n) 获取前n条查找到的记录，返回结果为元组嵌套元组， ((记录1),(记录2))，查询不到内容返回空元组。
> cur.fetchall() 获取所有查找到的记录，返回结果形式同上。



##### 		12.2.2.   写语句示例

```python
#各种数据操作部分-->写一条数据记录
try:
    sql ="update cls set score=%s where name = %s"
    cur.execute(sql,[71,"Lily"])
    db.commit()     # 提交
except Exception as e:
    print(e)
    db.rollback()       #回滚
```

```python
#各种数据操作部分-->写多条数据记录
list_data = [('Dave', 15, 'm', 81),
             ('Ala', 16, 'w', 82),
             ('Baron', 17, 'm', 83)]

try:
    sql = "insert into cls (name,age,sex,score) values (%s,%s,%s,%s)"
    cur.executemany(sql, list_data)  # 执行多次sql语句
    db.commit()  # 提交
except Exception as e:
    print(e)
    db.rollback()  # 回滚
```



#### 	12.3.   文件存储

- 存储文件路径
  - 优点：节省数据库空间，提取方便
  - 缺点：文件或者数据库发生迁移会导致文件丢失

- 存储文件本身
  - 优点：安全可靠，数据库在文件就在
  - 缺点：占用数据库空间大，文件存取效率低

- 代码示例：

  ```python
  # 各种数据操作-->存储图片数据
  file = open('sjch.jpg', 'rb')
  data = file.read()
  
  try:
      sql = "update cls set image=%s where name='Abby'"
      cur.execute(sql, data)
      db.commit()
  except:
      db.rollback()
  ```

  ```python
  #各种数据操作--> 提取文件
  sql = "select image from cls where name='Abby'"
  cur.execute(sql)
  data = cur.fetchone()  # 返回元祖
  file2 = open('mm.jpg', 'wb')
  file2.write(data[0])
  ```

---



# Network

