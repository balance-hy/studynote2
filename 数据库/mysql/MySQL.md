# MySQL

**FROM -> WHERE -> GROUP BY ->HAVING-> ORDER BY -> LIMIT**

## 服务启动与连接

![image-20231025161855329](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310251618922.png)

![image-20231025161923212](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310251619716.png)

## 创建数据库

**在创建数据库和表的时候，可以使用反引号来规避关键字**

![image-20231026141924509](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310261419355.png)

```sql
# 演示数据库操作
# 创建一个名称为balance的数据库 使用指令
CREATE DATABASE balance;# 默认字符集是utf8 mb4 排序规则是 utf8mb4_0900_ai_ci
# 删除数据库
DROP DATABASE balance;

# 创建一个名为balance01的数据库 设置字符集是utf8mb3 排序规则是 utf8mb3_bin
CREATE DATABASE balance01 CHARACTER SET utf8mb3 COLLATE utf8mb3_bin
```

##  删除和查询数据库

```sql
# 查看数据库语句 注意此处有s
SHOW DATABASES;

# 显示数据库创建语句 SHOW CREATE DATABASE db_name
SHOW CREATE DATABASE balance01;

# 数据库删除语句 DROP DATABASE [IF EXISTS] db_name
DROP DATABASE [IF EXISTS] balance01;
```

## 数据库的备份和恢复

![image-20231026145431090](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310261454383.png)

恢复数据库，也可以直接复制语句，执行一遍。

### 备份表

```shell
mysqldump -u 用户名 -p 数据库 表1 表2 表n > 文件名.sql
```

## 创建表/删除表

![image-20231026151805639](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310261518054.png)

```sql
CREATE TABLE `user`(
	id INT,# id INT UNSIGNED, 无符号
	userName VARCHAR(255),
	`password` VARCHAR(255),
	birthday DATE
)CHARACTER SET utf8mb3 COLLATE utf8mb3_bin ENGINE INNODB;

# 创建表还有一个快捷方式，当你想创建一个和现有的表结构相同的表的时候可以使用like
CREATE TABLE temp_tab LIKE tab_test

# 删除表
drop table xxx.xxx;# 第一个xxx为数据库名，第二个xxx为表名
```

## [数据类型](https://www.cnblogs.com/xrq730/p/8446246.html)

### 整型

**MySQL中整型默认是带符号的**。

| 数据类型  | 字节数 | 带符号最小值         |    带符号最大值     | 不带符号最小值 | 不带符号最大值       |
| :-------- | :----: | -------------------- | :-----------------: | -------------- | -------------------- |
| TINYINT   |   1    | -128                 |         127         | 0              | 255                  |
| SMALLINT  |   2    | -32768               |        32767        | 0              | 65535                |
| MEDIUMINT |   3    | -8388608             |       8388607       | 0              | 16777215             |
| INT       |   4    | -2147483648          |     2147483647      | 0              | 4294967295           |
| BIGINT    |   8    | -9223372036854775808 | 9223372036854775807 | 0              | 18446744073709551616 |

从实际开发的角度，我们**一定要为合适的列选取合适的数据类型**，即到底用不用得到这种数据类型？举个例子：

- 一个枚举字段明明只有0和1两个枚举值，选用TINYINT就足够了，但在开发场景下却使用了BIGINT，这就造成了资源浪费
- 简单计算一下，假使该数据表中有100W数据，那么总共浪费了700W字节也就是6.7M左右，如果更多的表这么做了，那么浪费的更多

要知道，**MySQL本质上是一个存储**，以Java为例，可以使用byte类型的地方使用了long类型问题不大，因为绝大多数的对象在程序中都是短命对象，方法执行完毕这块内存区域就被释放了，7个字节实际上不存在浪不浪费一说。但是MySQL作为一个存储，8字节的BIGINT放那儿就放那儿了，占据的空间是实实在在的。

### 浮点型 decimal

| **数据类型**            | **字节数** | **备注**                                                  |
| ----------------------- | ---------- | --------------------------------------------------------- |
| float                   | 4          | 单精度浮点型                                              |
| double                  | 8          | 双精度浮点型                                              |
| decimal(M，D)[UNSIGNED] |            | M为总位数(<=65)，D为小数位数(<=30),M默认或溢出时为10,D为0 |

我们总结一下float(M,D)、double(M、D)的用法规则：

- D表示浮点型数据小数点之后的精度，假如超过D位则四舍五入，即1.233四舍五入为1.23，1.237四舍五入为1.24
- M表示浮点型数据总共的位数，D=2则表示总共支持五位，即小数点前只支持三位数，所以我们并没有看到1000.23、10000.233、100000.233这三条数据的插入，因为插入都报错了

当我们不指定M、D的时候，会按照实际的精度来处理。

**float、double类型存在精度丢失问题**，即**写入数据库的数据未必是插入数据库的数据**，而decimal无论写入数据中的数据是多少，都不会存在精度丢失问题，这就是我们要引入decimal类型的原因，decimal类型常见于银行系统、互联网金融系统等对小数点后的数字比较敏感的系统中。

最后讲一下decimal和float/double的区别，个人总结**主要体现在两点上**：

- float/double在db中存储的是近似值，而decimal则是以字符串形式进行保存的
- decimal(M,D)的规则和float/double相同，但区别在float/double在不指定M、D时默认按照实际精度来处理而decimal在不指定M、D时默认为decimal(10, 0)

### 日期

| 数据类型  | 字节数 | 格式                | 备注                      |
| --------- | ------ | ------------------- | ------------------------- |
| date      | 3      | yyyy-MM-dd          | 存储日期值                |
| time      | 3      | HH:mm:ss            | 存储时分秒                |
| year      | 1      | yyyy                | 存储年                    |
| datetime  | 8      | yyyy-MM-dd HH:mm:ss | 存储日期+时间             |
| timestamp | 4      | yyyy-MM-dd HH:mm:ss | 存储日期+时间，可作时间戳 |

MySQL的时间类型的知识点比较简单，这里重点关注一下datetime与timestamp两种类型的区别：

- 上面列了，datetime占**8**个字节，timestamp占**4**个字节
- 由于大小的区别，datetime与timestamp能存储的时间范围也不同，datetime的存储范围为1000-01-01 00:00:00——9999-12-31 23:59:59，timestamp存储的时间范围为19700101080001——20380119111407
- **datetime默认值为空，当插入的值为null时，该列的值就是null；timestamp默认值不为空，当插入的值为null的时候，mysql会取当前时间**
- datetime**存储的时间与时区无关**，timestamp**存储的时间及显示的时间都依赖于当前时区**

在实际工作中，一张表往往我们会有两个默认字段，一个记录创建时间而另一个记录最新一次的更新时间，这种时候可以使用timestamp类型来实现：

```sql
create_time timestamp default current_timestamp comment "创建时间",
update_time timestamp default current_timestamp on update current_timestamp comment "修改时间",
```

### 字符

| 数据类型    | 字节长度 | 范围或用法                                              |
| ----------- | -------- | ------------------------------------------------------- |
| Char(M)     | M字符    | 定长字符串。意味着会存M个字符                           |
| VarChar(M)  | M字符    | 变长字符串，要求M<=255，意味着存实际字符的字节长度而非M |
| Tiny Text   | Max:255  | 大小写不敏感                                            |
| Text        | Max:64K  | 大小写不敏感                                            |
| Medium Text | Max:16M  | 大小写不敏感                                            |
| Long Text   | Max:4G   | 大小写不敏感                                            |

## 修改表

![image-20231027150549701](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310271505279.png)

### 添加列

在user表上增加一个image列，varchar类型，不允许为空，默认空字符串（在birthday之后）

```sql
ALTER TABLE `user` ADD image VARCHAR(32) NOT NULL DEFAULT '' AFTER birthday
```

### 修改列

修改user表的password字段长度，不允许为空，默认空字符串

```sql
ALTER TABLE `user` MODIFY `password` VARCHAR(100) NOT NULL DEFAULT ''
```

修改user表的password字段名，变为pwd

```sql
ALTER TABLE user01 CHANGE `password` pwd VARCHAR(60) NOT NULL DEFAULT '';
```

### 删除列

删除user表的image列

```sql
ALTER TABLE `user` DROP image
```

### 修改表名

将user表改为user01

```sql
RENAME TABLE `user` TO user01
```

### 修改表字符集

将user表字符集改为utf8

```sql
ALTER TABLE user01 CHARACTER SET utf8
```

## 增删改查

### insert

![](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310301305775.png)

```sql
CREATE TABLE `goods`(
	id INT,
	goods_name VARCHAR(10),
	price DOUBLE
)CHARACTER SET utf8mb3 COLLATE utf8mb3_bin ENGINE INNODB;

-- Insert添加数据
INSERT INTO goods (id,goods_name,price) VALUES(10,'huawei',2000);

-- Insert添加多条数据
INSERT INTO goods (id,goods_name,price) VALUES(10,'huawei',2000),(20,'vivo',3000),(30,'wei',4000);
-- 当插入单条数据时，value 的执行速度通常比 values 快。这意味着在插入单条数据的情况下，使用 value 更为高效。
-- 然而，当插入多条数据时，情况则相反，values 的执行速度通常比 value 快。这表明在批量插入数据时，使用 values 更为高效。

-- 若是给表中所有列添加数据，可以不写列名
INSERT INTO goods VALUES(40,'oppo',4000);

-- mysql中有两种方式插入数据
-- 使用 VALUES 关键字指定要插入的具体数值。
-- 使用 SELECT 语句从其他表中选择数据插入。
INSERT INTO tab_test (ename,job,sal) SELECT ename,job,sal FROM emp
```

![image-20231102132211504](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021322148.png)

### delete

![image-20231102135903373](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021359807.png)

```sql
-- 删掉 goods_name='huawei'的记录
delete from goods where goods_name='huawei';
-- 删掉所有记录
delete from goods;
```

### update

![image-20231102135241756](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021352313.png)

```sql
UPDATE goods SET price=10000 where goods_name='huawei';

-- 若无where限制，会更改所有
UPDATE goods SET price=10000;
-- 可以用基本运算
UPDATE goods SET price=price+1100;
-- 可以同时更改多个
UPDATE goods SET price=price+1100,id=id+100;
```

### select 单表

![image-20231102140639387](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021406415.png)

![image-20231102142410050](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021424708.png)

```sql
-- select 使用运算
SELECT `goods_name`,(id+price) FROM goods;

-- (id+price)会直接在结果里显示，可以取别名
SELECT `goods_name`,(id+price) AS total FROM goods;
```

#### 条件查询

![image-20231102143154908](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021431602.png)

| like 中通配符 | 作用                                                         |
| ------------- | :----------------------------------------------------------- |
| `%`           | 匹配零个或多个字符的任意字符串，like’Mc%’ 将搜索以字母 Mc 开头的所有字符串 |
| `_`           | 匹配任何单个字符，like’_heryl’ 将搜索以字母 heryl 结尾的所有六个字母的名称 |
| `[]`          | 指定范围 ([a-f]) 或集合 ([abcdef]) 中的任何单个字符          |
| `[^]`         | 不属于指定范围 ([a-f]) 或集合 ([abcdef]) 的任何单个字符      |
| `*`           | 同于DOS命令中的通配符，代表多个字符                          |
| `?`           | 同于DOS命令中的？通配符，代表单个字符                        |
| `#`           | 大致同上，不同的是代只能代表单个数字。k#k代表k1k,k8k,k0k     |

#### 排序

![image-20231102144433348](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311021444583.png)

```sql
-- price降序显示
SELECT * FROM goods ORDER BY price DESC;
```

#### 分组 groupby having

![image-20231105154055054](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051540746.png)

```sql
#演示group by + having
GROUPby用于对查询的结果分组统计，(示意图)
-- having子句用于限制分组显示结果.
-- ?如何显示每个部门的平均工资和最高工资
-- avg(sal) max(sal)
-- 按照部门来分组查询
 SELECT AVG(sal), MAX(sal),deptno
 FROM emp GROUP BY deptno;
-- ?显示每个部门的每种岗位的平均工资和最低工资
#分组的标准变成两个,先按照部门再按照岗位分
SELECT AVG(sal),MIN(sal),deptno,job
FROM emp GROUP BY deptno,job


-- ?显示平均工资低于2000的部门号和它的平均工资//别名
SELECT AVG(sal),deptno
FROM emp GROUP BY deptno
 HAVING AVG(sal)<2000
#使用别名,效率更高,函数不用计算两次
SELECT AVG(sal) AS avg_sal,deptno
FROM emp GROUP BY deptno 
HAVING avg_sal<2000
```

##### having 和 where

- having是对一个表的数据进行分组之后，对组信息进行相应条件筛选

  having筛选时，只能根据select子句中可出现的字段（数据）来进行条件设定

  having子句与where子句一样，都是用于条件判断

- where是判断数据从磁盘读入内存的时候，having是判断分组统计之前的所有条件

- having子句中可以使用字段别名，而where不能使用

#### 单表查询加强

##### where子句和like操作符

```sql
-- 使用where子句
-- 如何查找1986.1.1后入职的员工
#细节:注意时间格式
SELECT * FROM emp WHERE HiREDATE > '1986-01-01'

-- 如何使用like操作符
-- %:表示0到多个字符  _:表示单个字符
-- 如何显示首字符为S的员工姓名和工资
# 注意 需要用单引号包裹
SELECT * from emp WHERE ename like 'S%'

-- 如何显示第三个字符为大写O的所有员工的姓名和工资
SELECT * from emp WHERE ename like '__O%'

-- 如何显示没有上级的雇员的情况
#细节:判断是否为空要使用IS
SELECT * FROM emp WHERE mgr IS NULL

-- 查询表结构
#可以查询到字段和类型还有其他等等
DESC emp
```

小结 

* 注意时间格式
* 使用like时，查询需要用单引号包裹
* 判断是否为空 务必使用 IS 判断
* 查询数据表字段属性，可以使用 DESC 关键字

##### 排序

```sql
-- 使用order by子句
-- 如何按照工资的从低到高的顺序，显示雇员的信息
#默认升序 可以不写ASC
SELECT * FROM emp ORDER BY sal ASC

-- 按照部门号升序而雇员的工资降序排列,显示雇员信息
#先排部门 然后再在部门内部排工资
#细节:ORDER BY后面接条件,多个条件直接用逗号隔开即可
SELECT * FROM emp ORDER BY deptno ASC,sal DESC
```

小结 

* 排序默认升序
* 多个条件排序，逗号分割即可

##### 分页查询

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;`

```sql
-- 按雇员的empno号降序取出，每页 显示5条记录。请分别显示第3页，第5页对应的sql语句
#细节:公式:LIMIT 每页要显示的记录数*(第几页-1), 每页要显示的记录数
#第3页
SELECT * FROM emp ORDER BY empno DESC LIMIT 10,5
#第5页
SELECT * FROM emp ORDER BY empno LIMIT 20,5
```

小结 

* limit所限制的应放在语句最后

执行顺序

**FROM -> WHERE -> GROUP BY ->HAVING-> ORDER BY -> LIMIT**

##### 分组增强

```sql
-- (1)显示每种岗位的雇员总数、平均工资。
SELECT job,COUNT(*) as number,AVG(sal) as avg FROM emp GROUP BY job 
-- (2)显示雇员总数，以及获得补助的雇员数。
# 细节:count函数默认不统计NULL,所以NULL自动被剔除
SELECT count(*),count(IF(comm>0,1,NULL)) FROM emp

-- (3)显示管理者的总人数。
SELECT COUNT(DISTINCT mgr) FROM emp

-- (4)显示雇员工资的最大差额。
SELECT MAX(sal)-MIN(sal) FROM emp
```

小结

* count(*)是返回满足条件的记录数
  count(列)是返回列满足条件的记录数，但排除NULL的情况
* 如果要在统计时判断，**可以使用流程控制函数 if 等**
* 去重可以使用 DISTINCT

##### 多子句查询

```sql
-- 应用案例:请统计各个部门group by 的平均工资avg,
-- 并且是大于1000的having， 并且按照平均工资从高到低排序，order by
-- 取出前两行记录limit 0，2
#细节:limit公式:LIMIT 每页要显示的记录数*(第几页-1), 每页要显示的记录数
SELECT deptno,AVG(sal) as avgsal FROM emp 
GROUP BY deptno 
HAVING avgsal > 1000 
ORDER BY avgsal DESC 
LIMIT 0,2
```

### select 多表

#### 多表笛卡尔积

合并查询（笛卡尔积，会展示所有组合结果）：
`select * from employee, dept;`.

在默认情况下：当两个表查询时，规则

* 从第一张表中，取出一行和第二张表的每一行进行组合，返回结果[含有两张表的所有列]，

* 一共返回的记录数**第一张表行数*第二张表的行数**

* **这样多表查询默认处理返回的结果，称为笛卡尔集**

* 解决这个多表的关键就是要**写出正确的过滤条件where**，需要程序员进行分析

#### 多表查询

```sql
-- 多表查询练习many tab.sql
-- 显示雇员名,雇员工资及所在部门的名字[笛卡尔集]
# 注意若有字段两张表都有，需要指定
SELECT ENAME,SAL,DNAME,emp.DEPTNO 
		FROM emp,dept
		WHERE emp.DEPTNO=dept.DEPTNO

-- 老韩小技巧:多表查询的条件不能少于表的个数-1,否则会出现笛卡尔集
-- 如何显示部门号为10的部门名、员工名和工资
#细节:若果使用having,having要在where后面
SELECT ENAME,SAL,DNAME,emp.DEPTNO 
		FROM emp,dept
		WHERE emp.DEPTNO=dept.DEPTNO AND emp.DEPTNO=10

-- 显示各个员工的姓名，工资，及其工资的级别
# 过滤条件要想清楚
# 工资级别有规定范围,那就筛选员工表的工资落到的范围之内即可
SELECT ENAME,SAL,GRADE
		FROM emp,salgrade
		WHERE emp.SAL >=salgrade.LOSAL AND emp.SAL <=salgrade.HISAL
		
SELECT ENAME,SAL,GRADE
		FROM emp,salgrade
		WHERE SAL BETWEEN LOSAL AND HISAL ORDER BY SAL DESC
```

#### 自连接

自连接是指在同一张表的连接查询（将同一张表看做两张表）

**使用自连接注意需要起别名**

```sql
-- 思考题:显示公司员工和他的上级的名字
# 直接使用 SELECT * FROM emp,emp 会报错 表名不唯一 所以需要起别名
SELECT worker.ENAME AS '员工' , boss.ENAME AS '上司'
		FROM emp worker,emp boss
		WHERE worker.MGR = boss.EMPNO
```

![image-20240331150115037](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240331150115037.png)

#### 多行子查询

什么是子查询？**子查询就是嵌入在其它sql语句中的select语句，也叫嵌套查询**

**单行子查询：只返回一行**

```sql
-- 请思考:如何显示与SMITH同一部门的所有员工?
-- 思路:先查到smith的所在部门,通过该部门号查询该部门员工
SELECT DEPTNO
		FROM emp
		WHERE ENAME='SMITH'

# 上面的语句作为子查询 因为它只返回一行，故称单行子查询

SELECT * FROM emp
			WHERE DEPTNO=(SELECT DEPTNO FROM emp WHERE ENAME='SMITH')
```

**多行子查询：返回多行 使用关键字 in**

```sql
-- 课堂练习:如何查询和部门10的工作相同的雇员的
-- 名字、岗位、工资、部门号，但是不含10号部门的员工.

# 第一步查询10号部门有哪些工作 注意去重
SELECT DISTINCT JOB FROM emp WHERE DEPTNO=10
		
# 完善整个查询
SELECT ENAME,JOB,SAL,DEPTNO
		FROM emp
		WHERE DEPTNO != 10 AND JOB IN (
			SELECT DISTINCT JOB FROM emp WHERE DEPTNO=10
		)
# DEPTNO != 10 也可以写成 DEPTNO <> 10
```

##### all和any

- any表示任何一个匹配就为真
- all要所有匹配才为真

```sql
-- 请思考:显示工资比部门30的所有员工的工资都高的员工的姓名、工资和部门号
#思路:要求使用all
SELECT ENAME,SAL,DEPTNO
		FROM emp
		WHERE SAL > ALL(SELECT SAL FROM emp WHERE DEPTNO=30)

# 使用 MAX
SELECT ENAME,SAL,DEPTNO
		FROM emp
		WHERE SAL > (SELECT MAX(SAL) FROM emp WHERE DEPTNO=30)

-- 请思考:如何显示工资比部门30的其中一个员工的工资高的员工的姓名、工资和部门号
#思路:这里要求使用any
#把30部门的工资查询出来然后使用any再和其他部门比较即可
SELECT ENAME,SAL,DEPTNO
		FROM emp
		WHERE SAL > ANY(SELECT SAL FROM emp WHERE DEPTNO=30)

#另一种思路直接比较30部门工资最低的即可
SELECT ENAME,SAL,DEPTNO
		FROM emp
		WHERE SAL > (SELECT MIN(SAL) FROM emp WHERE DEPTNO=30)
```

#### 子查询临时表

**子查询的结果当做临时表使用 ** 很多问题可以通过这种方法解决

```sql
SELECT * FROM emp,(SELECT DEPTNO
		FROM emp
		WHERE ENAME='SMITH') deptsmith
```

用括号括起来即可

#### 多列子查询

返回多个列的子查询称为多列子查询

**注意多列的比较需要用括号括起来**

```sql
-- 练习1:请思考如何查询与smith的部门和岗位完全相同的所有雇员(并且不含smith本人

#1.先查到smith在哪个岗位和部门

SELECT DEPTNO,JOB FROM emp WHERE ENAME='SMITH'

#通过多列进行比较获取和他同部门和同岗位的员工
#把上面的查询当做子查询来使用，并且使用多列子查询的语法进行匹配

SELECT * FROM emp WHERE ENAME !='SMITH' AND
		(DEPTNO,JOB)=(SELECT DEPTNO,JOB FROM emp WHERE ENAME='SMITH') 
```

#### 子查询练习

注意，**在多张表查询时，如果要显示某张表的所有字段可以使用 表名.* 也是可以**

```sql
-- 请思考:查找每个部门工资高于本部门平均工资的人的资料
-- 这里要用到数据查询的小技巧，把一个子查询当作一个临时表使用
#思路:
#    1.先查询部门的平均工资,当然也要进行分组
#    2.使用where子句筛选本部门工资大于平均工资的
#    3.进行比较是要对应条件
SELECT DEPTNO,AVG(SAL) AS avgsal FROM emp GROUP BY DEPTNO

SELECT EMPNO,ENAME,JOB,MGR,HIREDATE,SAL,COMM,emp.DEPTNO
		FROM emp , (SELECT DEPTNO,AVG(SAL) as avgsal FROM emp GROUP BY DEPTNO) sales
		WHERE emp.DEPTNO=sales.DEPTNO AND emp.SAL > avgsal
		
# ---------------------------------------------------------------
-- 请思考:查找每个部门工资最高的人的详细资料
SELECT DEPTNO,MAX(SAL) as max_sal FROM emp GROUP BY DEPTNO

SELECT EMPNO,ENAME,JOB,MGR,HIREDATE,SAL,COMM,emp.DEPTNO
		FROM emp,(SELECT DEPTNO,MAX(SAL) as max_sal FROM emp GROUP BY DEPTNO) sales
		WHERE emp.DEPTNO=sales.DEPTNO AND SAL=max_sal

#----------------------------------------------------------------
-- 查询每个部门的信息(包括:部门名;编号,地址)和人员数量
SELECT DEPTNO,count(*) FROM emp GROUP BY DEPTNO

SELECT dept.DEPTNO,DNAME,LOC,num
		FROM dept,(SELECT DEPTNO,count(*) AS num FROM emp GROUP BY DEPTNO) temp
		WHERE dept.DEPTNO=temp.DEPTNO

		
#------------------------------------------------------------------
```

#### 表复制以及表去重

自我复制数据(蠕虫复制) 

有时，**为了对某个sql语句进行效率测试，我们需要海量数据时，可以使用此法为表创建海量数据**

```sql
INSERT INTO tab_test SELECT * FROM tab_test
```

这样是指数级增长，非常快

**如何删除一个表的重复数据**

* 先创建一个和现有表结构相同的表

  * ```sql
    CREATE TABLE 临时表 LIKE 现有表
    ```

* 将去重后的数据加入临时表 **使用`DISTINCT * `去重**

  * ```sql
    INSERT INTO 临时表 SELECT DISTINCT * FROM 现有表
    ```

* 删除原表，修改表名 或者 删除原表所有数据，将临时表的数据加入原表

  * ```sql
    Drop table 现有表
    Rename table 临时表名 to 现有表名
    ```

  * ```sql
    delete from 现有表
    INSERT INTO 现有表 SELECT * FROM 临时表
    ```

#### 合并查询

介绍有时在实际应用中，为了合并多个select语句的结果，可以使用集合操作符号 union,union all

**union all**

不会去重，只是简单合并

**union**

会自动去重合并 其实就是 并集-交集

#### 外连接 where->on

现有需求

**列出部门名称和这些部门的员工信息(名字和工作)，同时列出那些没有员工的部门。**

如果使用传统多表查询

```sql
SELECT DNAME,ENAME,JOB
		FROM emp,dept
		WHERE emp.DEPTNO=dept.DEPTNO
		ORDER BY DNAME ASC
```

发现只有三个部门，实际上有四个部门，只不过第四个部门没有员工

**因为where子句是根据关联条件，显示所有匹配记录，匹配不上的，不显示**

这时，我们就可以使用外连接来完成

**外连接**

* 左外连接（如果**左侧的表完全显示**我们就说是左外连接）
  * `select .. from 表1 left join 表2 on 条件 [表1:就是左表 表2:就是右表]`
* 右外连接（如果**右侧的表完全显示**我们就说是右外连接）
  * `select .. from 表1 right join 表2 on 条件[表1:就是左表 表2:就是右表]`

至此，就可以完成上面的需求了

```sql
SELECT DNAME,ENAME,JOB
		FROM emp RIGHT JOIN dept
		ON emp.DEPTNO=dept.DEPTNO
		ORDER BY DNAME ASC
```

## Mysql约束

约束用于确保数据库的数据**满足特定的规则**。

在mysql中，约束包括：

* not null 非空
* unique 唯一
* primary key 主键
* foreign key 外键
* check 检查

### 主键 primary key

用于唯一的标示表行的数据，**当定义主键约束后，该列不能重复**

```
字段名 字段类型 primary key
```

**注意事项：**

* primary key不能重复而且不能为null

* 一张表最多只能有一个主键，但可以是**复合主键(字段定义时不指定，最后指定如 primary key(id,name))**

  * ```sql
    CREATE TABLE t6 (
        id INT, 
        `name` VARCHAR(32),
        age INT ,
        PRIMARY KEY(id,`name`)
    )
    ```

* 主键的指定方式有两种

  * 直接在字段名后指定 `字段名 primakry key`
  * 在表定义最后写`primary key(列名)`

* 使用`desc 表名`，可以看到primary key的情况

### 外键 foreign key

用于定义主表和从表之间的关系：

* 外键约束要定义在从表上，主表则必须具有主键约束或是unique约束
* 当定义外键约束后，要求**外键列数据必须在主表的主键列存在或是为null**

![在这里插入图片描述](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/103bf578fc9545878701988a9c307c66.png)

```sql
FOREIGN KEY(本表字段名）REFERENCES 主表名（主键名或unique字段名）
            
-- 创建主表 my_class
CREATE TABLE my_class (
	id INT PRIMARY KEY,#班级编号
	`name` VARCHAR(32) NOT NULL DEFAULT ''
); 

-- 创建从表
CREATE TABLE my_stu (
	id INT PRIMARY KEY, #学生编号
	`name` VARCHAR(32) NOT NULL DEFAULT '',
	class_id INT, #对应的班级编号
	FOREIGN KEY(class_id) REFERENCES my_class(id)
);  
```

**注意：**

* 外键指向的表的字段， 要求是**primary key或者是unique**
* **表的类型是 innodb** , 这样的表才支持外键
* 外键字段的类型要和主键字段的**类型一致**(长度可以不同)
* 外键字段的值，必须在主键字段中出现过，或者为null [前提是外键字段允许为nul]
* 一旦建立主外键的关系，数据不能随意删除，需要**先删除主表对应的所有从表的外键约束的字段，才可以删除主表字段**

### 非空 not null

如果在列上定义了notnull,那么当插入数据时，必须为列提供数据。

```
字段名 字段类型 not null
```

### 唯一 unique

当定义了唯一约束后，该列值是不能重复的.。

```
字段名 字段类型 unique
```

**注意：**

* 如果未定义非空，实际上**可以存在多个null**

### check 检查

**用于强制行数据必须满足的条件**。

假定在sal列上定义了check约束，并要求sal列值在1000～2000之间，如果不再1000～2000之间就会提示出错。

oracle和sqlserver均支持check，**mysql5.7不支持check，只做语法校验，但不会生效，8.0.16后支持**

```
列名 类型 check (check条件)
```

### 自增

在某张表中，存在一个id列（整数），我们希望在添加记录的时候该列从1开始，自动的增长，怎么处理？

```sql
字段名  整型   primary key auto_increment 
```

**自增长使用细节**

* 一般来说自增长是和primary key配合使用的
* 自增长也可以单独使用[但是需要配合一个unique]
* 自增长修饰的字段为整数型的(虽然小数也可以，但是非常非常少这样使用）
* 自增长默认从1开始，你也可以通过如下命令修改
  * `alter table 表名 auto_increment =新的开始值`;
* 如果你添加数据时，给自增长字段(列)指定了值，则以指定的值为准

## Mysql索引

说起提高数据库性能，索引是最物美价廉的东西了。不用加内存，不用改程序，不用调sql，查询速度就可能提高百倍千倍。

```sql
SELECT * FROM emp WHERE ename='axJxC' #未添加索引前查询时间5.7秒
#创建索引
-- create index 索引名 on 表名(字段列名)
CREATE INDEX ename_index ON emp(ename) #创建索用了25秒
SELECT * FROM emp WHERE ename='axJxC' #创建索引后查询时间0.002秒
```

提出问题：是不是建立一个索引就能解决所有的问题？ename 上没有建立索引会怎样？

**索引只对当前列生效，对未建立索引的列不生效，建立索引后，占用的空间变大**

### 索引机制

* **没有索引为什么会慢**？
  ​ 因为要进行**全表扫描**。

* **使用索引为什么会快**？
  ​ 形成一个**索引的数据结构**，比如二叉树（只是举例）。

**INNODB引擎默认索引类型为 B+ 树**

![在这里插入图片描述](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/5f389b1300994d859e2e6dd99daf729a.png)

#### 索引的代价

1. **磁盘占用** 即占用空间变大，空间换时间
2. 增加**dml**(Data Manipulation Language 即update delete insert)语句的**效率影响**。因为需要重新维护索引
   但在我们项目中， select[**90%**]比update，delete，insert**[10%**]操作多。

### 索引的类型

1. 主键索引，**主键自动的为主索引(类型Primary key)**

2. 唯一索引(UNIQUE)  

   1. ```sql
      id int unique,  -- id唯一，同时也是索引，称为unique索引
      ```

3. 普通索引(INDEX)

4. 全文索引(FULLTEXT) [适用于MyISAM] 一篇文章搜索某个单词
   开发中考虑使用：全文搜索Solr 和 ElasticSearch (ES)

5. 查询索引：**SHOW INDEX FROM 表名**

### 索引的操作

#### 索引创建

```sql
#创建新表时没有添加主键
CREATE TABLE t13(
id INT ,
`name` VARCHAR(32)
)
#添加主键索引方法:在创建表的时候没有添加,就另外alter
ALTER TABLE t13 ADD PRIMARY KEY (id)

-- 添加唯一索引
#方法1
CREATE UNIQUE INDEX id_index ON t13(id)

#方法2:在创建表的时候添加unique关键字
CREATE TABLE t14(
id INT UNIQUE,
`name` VARCHAR(32) 
)

#查询索引
SHOW INDEXES FROM t13   # id是主键索引,name是唯一索引

-- 添加普通索引
# 方法1
CREATE INDEX id_index ON t12(id)
# 方法2
ALTER TABLE t12 ADD INDEX id_index (id)
```

如果某列的值，不会重复，则优先考虑使用 unique 索引，否则使用普通索引

#### 索引删除

```sql
-- 删除索引演示
#删除普通索引
DROP INDEX id_index ON t12

#删除唯一索引 同理
DROP INDEX id_index ON t12

#删除主键索引
#细节:以这种方式 drop index primary key on t12 是删除不了主键索引
ALTER TABLE t12 DROP PRIMARY KEY #正确删除主键索引的方式
```

#### 索引查找

```sql
-- 查询索引
#方法1
SHOW INDEX FROM 表名
#方法2
SHOW INDEXES FROM 表名
#方法3
SHOW KEYS FROM 表名
#方法4 :不够精确
DESC 表名
```

#### 索引修改

修改索引就是先删除原有，再添加就可以

### 索引使用场合

- 较频繁的作为查询条件字段应该创建索引，例如序号
- **唯一性太差的字段不适合单独创建索引**，即使频繁作为查询条件，例如性别
- **更新非常频繁的字段不适合创建索引**，例如登录次数
- 不会出现在WHERE子句中字段不该创建索引

## Mysql事务

**什么是事务**

- 事务用于保证数据的一致性，它由一组相关的DML语句组成， 该组的dml语句**要么全部成功，要么全部失败**。如: 转账就要用事务来处理，用以保证数据的一致性。

- 数据库的**事务（Transaction）**是一种机制、一个操作序列，包含了一组数据库操作命令。事务把所有的命令作为一个整体一起向系统提交或撤销操作请求，即这一组数据库命令要么都执行，要么都不执行，因此事务是一个不可分割的工作逻辑单元。


**ACID**

- 原子性(Atomicity)

  - 原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
  
- 一致性 (Consistency)  **用户保证而非数据库系统保证**
  - 事务必须使数据库从一个正确的一致性状态变换到另外一个正确一致性状态。
  
  - ```
    A要向B支付100元,而A的账户中只有90元,我们的账户余额列没有任何约束.然后支付成功了.
    
    这里,如果按照很多人的理解,事务不是保证一致性么?直观上账户余额为什么能为负呢.但这里事务执行前和执行后,我们的系统没有任何的约束被破坏.一直都是保持正确的状态.
    
    应用系统从一个正确的状态到另一个正确的状态.而ACID就是说事务能够通过AID来保证这个C的过程.C是目的,AID都是手段
    ```
  
- 隔离性(Isolation)
  - 事务的隔离性是多个用户并发访问数据库时，数据库为每一-个用户开启的事务，不能被其他事务的操作数据所干扰，多个并发事务之间要相互隔离。
  
- 持久性(Durability)
  - 持久性是指一个事务一旦被提交， 它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响。

**事务和锁**

- **当执行事务操作时(dml语句) ，mysql会在表上加锁，防止其它用户改表的数据。**
- 回退事务
  - 保存点(savepoint)是事务中的点，用于取消部分事务，**当结束事务时(commit) ，会自动的删除该事务所定义的所有保存点**。当**执行回退事务时，通过指定保存点可以回退到指定的点**
- 提交事务
  - 使用commit语句可以提交事务，当执行commit语句后，**会确认事务的变化、结束事务、删除保存点、释放锁，数据生效**。当使用**commit语句结束事务后**，其它会话[其他连接]将可以**查看到事务变化后的新数据[所有数据就正式生效]**

锁用于确保事务完整性和数据库一致性。 锁可以防止用户读取其他用户正在更改的数据，并防止多个用户同时更改相同的数据。 如果不使用锁，数据库中的数据可能在逻辑上变得不正确，而针对这些数据进行查询可能会产生想不到的结果。

在DBMS中，可以按照锁的粒度把数据库锁分为**行级锁(INNODB引擎)、表级锁(MYISAM引擎)和页级锁(BDB引擎 )。**

### 事务操作

![image-20240410105636478](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410105636478.png)

![img](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/3e64729f58dd48678c3e99dd97a5f23d.png)

```sql
-- 事务操作
# 1.创建表
CREATE TABLE trans(
		id INT,
		name VARCHAR(255)
);
# 2.开始一个事务
START TRANSACTION

# 3.设置保存点 
# 语法 :SAVEPOINT 保存点名字
SAVEPOINT a

# 4.操作表,更新表的数据
INSERT INTO trans VALUES(1,'25ren')
SELECT * FROM trans
 
# 5.设置保存点把数据保存
SAVEPOINT b

# 6.再插入一条数据
INSERT INTO trans VALUES(2,'renren')
SELECT * FROM trans

# 7.回退事务
#回退到第一次保存的只有一条数据的点
# ROLLBACK TO 保存点名
ROLLBACK to b
SELECT * FROM trans


# 8.不指定名字回退全部事务
ROLLBACK
SELECT * FROM trans

# 9.提交事务,所有操作生效,不能回退
COMMIT
SELECT * FROM trans
```

#### 事务注意事项

- 如果不开始事务，**默认情况下，dml操作是自动提交的，不能回滚**。

- 如果开始一个事务，你没有创建保存点,，你可以执行``rollback`,默认就是回退到你事务开始的状态。
- 你也可以在这个事务中(还没有提交时)，创建多个保存点.比如: savepoint aaa; 执行dml , savepoint bbb;。
- 你可以在事务没有提交前，选择回退到哪个保存点。
- mysql的**事务机制需要innodb的存储引擎才可以使用**，myisam不好使。
- 开始一个事务的两种语法：`start transaction;  set autocommit= off;`

### 4种隔离级别

事务隔离级别介绍:**多个连接开启各自事务操作数据库中数据时，数据库系统要负责隔离操作，以保证各个连接在获取数据时的准确性**。如果**不考虑隔离性，可能会引发如下问题**：

* 脏读 （**A事务读取B事务还未提交的值**）
  * 当一个事务读取另一个事务尚未提交的修改时，产生脏读
* 不可重复读 (**不能重复读到同一个值**)
  * 同一查询在同一事务中多次进行，由于其他提交事务所做的**修改或删除**，每次返回不同的结果集，此时发生不可重复读。
* 幻读 （**每次返回的结果集大小不同**）
  * 同一查询在同一事务中多次进行，由于其他提交事务所做的**插入操作**，每次返回不同的结果集，此时发生幻读。

事务隔离级别概念：Mysq隔离级别定义了**事务与事务之间的隔离程度**

| Mysql隔离级别              | 脏读 | 不可重复读 | 幻读 | 加读锁   |
| -------------------------- | ---- | ---------- | ---- | -------- |
| 读未提交                   | ✔️    | ✔️          | ✔️    | 不加锁   |
| **读已提交**               | ✖️    | ✔️          | ✔️    | 不加锁   |
| 可重复读                   | ✖️    | ✖️          | ✖️    | 不加锁   |
| **可串行(Serializable)化** | ✖️    | ✖️          | ✖️    | **加锁** |

注意可串行化是加锁，所以会堵塞。当A事务在读一个资源，B事务加入并操作该资源，当B事务修改之后，会堵塞，直至B事务提交。

**事务隔离级别设置**：

* 查看**当前会话**隔离级别 `select @@transaction_isolation;`
* 查看**系统**当前隔离级别 `select @@global.transaction_isolation;`
* **设置**当前会话隔离级别 `SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ; #设置为可重复读`
* **设置**系统当前隔离级别 `SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ; #设置为可重复读`

mysql **默认的事务隔离级别是`repeatable read `**,一般情况下，没有特殊要求，没有必要修改(因为该级别可以满足绝大部分项目需求)

若要全局修改隔离级别，可以**修改 my.ini 文件**

在文件最后加上下面语句`transaction-isolation = REPEATA BLE-READ #修改为可重复读`

可选参数有: READ-UNCOMMITTED, READ-COMMITTED, REPEATABLE-READ,SERIALIZABLE

**注:非必要不修改**

## Mysql存储引擎

Mysql 8 中一共有 11 种存储引擎

| 存储引擎           | 作用                                                         |
| ------------------ | ------------------------------------------------------------ |
| ARCHIVE            | 专门用于存储归档数据，压缩数据存储，**适合保存大量历史数据，行级锁** |
| BLACKHOLE          | 不保存数据，作为一个占位符，用于测试或复制                   |
| **CSV**            | 将数据存储为 CSV 文件，适用于需要将数据导出到其他程序的场景  |
| **InnoDB**         | **支持事务、行级锁定和外键约束**，提供崩溃恢复功能，支持自适应哈希索引，提高查询性能 **64TB** |
| **MEMORY**         | 数据**存储在内存中**，速度快，用于临时数据和缓存 **表级锁  无法持久化** |
| MRG_MYISAM         | 将多个 MyISAM 表合并为一个逻辑表来进行查询。这样，你可以像查询单个表那样查询这个合并后的表 |
| **MylSAM**         | **不支持事务和行级锁定**，表级锁定。比 InnoDB 更适合用于**读多写少**的场景 |
| PERFORMANCE SCHEMA | 该存储引擎提供了一组系统表，可以通过查询这些表来了解服务器的性能、资源使用情况、锁的等待和竞争等信息。 |
| FEDERATED          | 用于连接到远程数据库。允许在本地数据库上查询远程数据库中的表。 |
| NDBInfo            | NDBInfo 是一个用于监控和管理 NDBCluster 的工具。它提供了一个 MySQL 存储引擎，可以从 MySQL Cluster 中收集有关运行状态的信息 |
| NDBcluster         | MySQL 的一个存储引擎，专门为分布式和高可用性设计             |

**主要注意 InnoDB MEMORY MylSAM**

除了  InnoDB 其它都不支持事务，查看所有引擎 `show engines`

三种常用引擎细节

* MyISAM**不支持事务、 也不支持外键**，但其访问速度快，对事务完整性没有要求
* InnoDB存储引擎提供 了具有提交、回滚和崩溃恢复能力的事务安全。但是比起MyISAM存储引擎，InnoDB写的处理效率差一些并且会占用更多的磁盘空间以保留数据和索引。
* MEMORY存储引擎使用存在内存中的内容来创建表。每个MEMORY表只实际对应一个磁盘文件。MEMORY类型的表访问非常得快，因为它的数据是放在内存中的，并且默认使用HASH索引。但是**一旦服务关闭，表中的数据就会丢失掉，表的结构还在**。

手动修改存储引擎： `ALTER TABLE 表名 ENGINE = 储存引擎;`

## Mysql视图

视图（View）是一个**基于查询结果的虚拟表**，视图与真实表的区别在于视图本质上并不存储数据，**它是一个查询结果的呈现方式**，而真实表则实际存储数据。因此，**对视图的更改不会影响底层表的内容。**

**简而言之，其实就是封装了一个 VO 对象**







## Mysql用户管理





## 数据库范式

数据库范式（Database Normalization）是设计数据库时对数据表结构进行优化的一个方法。其**目的是消除数据冗余、减少数据异常并确保数据一致性**。范式的原则是确保数据的逻辑组织合理，方便维护和查询。一般来说，数据库设计中有五种主要范式，它们是：

1. **第一范式（1NF）**：
   - 表中的所有列必须是原子值，不可再分。
   - 所有的记录具有相同数量的字段。
   - 去除重复记录，确保表中没有重复的行。
2. **第二范式（2NF）**：
   - 符合第一范式。
   - 表中的所有非主键列都完全依赖于主键。
   - 消除部分依赖，即非主键列不依赖于主键的部分。
3. **第三范式（3NF）**：
   - 符合第二范式。
   - 消除传递依赖，即非主键列不依赖于其他非主键列。
   - 确保每一列都直接依赖于主键，而不是通过其他列间接依赖于主键。
4. **BCNF（Boyce-Codd范式）**：
   - BCNF是第三范式的扩展。
   - 表中的每个属性都必须是对候选键的完全函数依赖。
   - 这意味着表中没有任何列依赖于非候选键。
5. **第四范式（4NF）**：
   - 符合BCNF。
   - 消除多值依赖。
   - 确保没有一对多或多对多的多值依赖。
6. **第五范式（5NF）**：
   - 符合第四范式。
   - 消除连接依赖。
   - 关注实体之间的关系分解，确保数据没有任何多余的关系。

范式越高，数据的冗余度越低，数据维护的复杂度也会降低。但是，高范式会导致数据库的查询复杂度增加，并且可能导致性能的下降。因此，在设计数据库时，需要权衡数据的规范化程度和性能的需求，适度选择合适的范式。



## 各类函数

### 统计函数count

![image-20231105152243004](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051522391.png)

```sql
-- 统计有多少条记录
SELECT COUNT(*) FROM goods;

-- 条件过滤
SELECT COUNT(*) FROM goods WHERE price>9999;

-- count(*)和count(列)的区别
-- count(*)是返回满足条件的记录数
-- count(列)是返回列满足条件的记录数，但排除NULL的情况
```

### 和函数 sum

![image-20231105153059797](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051531508.png)

```sql
-- 单列的统计，并取别名
SELECT SUM(price) AS total FROM goods;

-- 多列的统计，并取别名
SELECT SUM(id) AS id_total,SUM(price) AS price_total FROM goods;
```

### 最大最小值 max/min

![image-20231105153736765](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051537021.png)

### 平均值 avg

![image-20231105153810461](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051538770.png)

### 字符串函数

标红意味着常使用

![image-20231105162228386](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202311051622664.png)

```sql
-- CHARSET(str)    返回字串字符集
SELECT CHARSET(ename) FROM emp #utf8
-- CONCAT (string2 [,... ])    连接字串
SELECT CONCAT(ename,' job is ',job) FROM emp; #把名字和工作连接在一起

-- INSTR (string ,substring )    返回substring在string中出现的位置，没有返回0，下标从1开始
# DUAL叫亚元表,系统表,可以作为测试使用
SELECT INSTR('balance','an') FROM DUAL; #返回4

-- UCASE (string2 )    转换成大写
SELECT UCASE(ename) FROM emp

-- LCASE (string2 )    转换成小写
SELECT LCASE(ename) FROM emp

-- LEFT (string2,length )    从string2中的左边起取length个字符
-- RIGHT 右边也可以，类似
SELECT LEFT(ename,1) FROM emp

-- SUBSTRING (str，position [, length]) 从str的position开始[从1开始计算] ,取length个字符
SELECT SUBSTRING(ename,2,2) FROM emp #从第二个开始获取两个字符 注意最后一个参数不给，
														# 选取结尾所有

-- LENGTH (string )    string长度[按照字节]
SELECT LENGTH (ename) FROM emp

-- REPLACE (str ,search_ str,replace__str )     在str中用replace_ str 替换search_ str
SELECT REPLACE('balance','ance','high') FROM DUAL; # balhigh

-- STRCMP (string1 ,string2 )    逐字符比较两字串大小，
SELECT STRCMP('balance','ance') FROM DUAL;  #返回1,-1,0

-- LTRIM (string2 ) RTRIM (string2 )    去除左端空格或右端空格
-- trim 去除左端和右端空格
#去除左端空格
SELECT LTRIM('  张无忌学java') FROM DUAL
#去除右端空格
SELECT RTRIM('  张无忌学java   ') FROM DUAL

SELECT TRIM(' 张无忌学java ') FROM DUAL
#细节1:即使没有空格也不会报错
#注意:函数后面不要带空格再接括号
```

以首字母小写的方式显示所有员工emp表的姓名

```sql
# 截取拼接-left right
SELECT CONCAT(LCASE(LEFT(ename,1)),RIGHT(ename,LENGTH(ename)-1)) FROM emp;
# 截取拼接-substring
SELECT CONCAT(LCASE(SUBSTRING(ename,1,1)),SUBSTRING(ename,2)) FROM emp;
```

### 数学函数

| 函数                               | 功能                             |
| ---------------------------------- | -------------------------------- |
| CEIL(x)                            | 向上取整                         |
| FLOOR(x)                           | 向下取整                         |
| MOD(x, y)                          | 返回x/y的模                      |
| RAND()                             | 返回0~1内的随机数                |
| ROUND(x, y)                        | 求参数x的四舍五入值，保留y位小数 |
| ABS(num)                           | 绝对值                           |
| BIN (decimal number)               | 十进制转二进制                   |
| FORMAT (number,decimal _places )   | 保留小数位数                     |
| CONV( number2,from_ base,to_ base) | 进制转换                         |
| HEX (DecimalNumber)                | 转十六进制                       |
| LEAST (number , number2…)          | 求最小值                         |

```sql
-- 数学函数
-- ABS(num)        绝对值
SELECT ABS(-10) FROM DUAL #10
-- BIN (decimal_ number )    十进制转二进制
SELECT BIN(20) FROM DUAL #10100

-- CEILING (number2 )    向上取整，得到比num2大的最小整数
SELECT CEILING(1.3) FROM DUAL #2

-- CONV( number2,from_ base,to_ bas)    进制转换
#把12从10进制转成8进制
SELECT CONV(12,10,8) FROM DUAL #14

-- FLOOR (number2 )    向下取整，得到比num2小的最大整数
SELECT FLOOR(-1.2) FROM DUAL #-2

-- FORMAT (number,decimal_ places )    保留小数位数(四舍五入)
SELECT FORMAT(12.3456,2) FROM DUAL #把12.3456保留两位小数 12.34

-- HEX (DecimalNumber )        转十六进制
SELECT HEX(22) FROM DUAL #16
-- LEAST (number，number2 [..])    求最小值
SELECT LEAST(2,5,1,6) FROM DUAL #1
-- MOD (numerator ,denominator )    求余
#10对3取余,得到1
SELECT MOD(10,3) FROM DUAL

-- RAND( [seed] )    RAND([seed])    其范围为0≤v≤1.0

SELECT RAND() FROM DUAL #随机生成0-1之间的小数,再次运行会改变
SELECT RAND(10) FROM DUAL #放了种子之后生成随机数之后再运行不会改变,要重新切换种子
```

### 时间函数

| 函数                               | 功能                                              |
| ---------------------------------- | ------------------------------------------------- |
| CURRENT_DATE ( )                   | 当前日期                                          |
| CURRENT_TIME ( )                   | 当前时间                                          |
| DATE(datetime)                     | 返回datetime的日期部分                            |
| DATE_ADD(date, INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATE_SUB(date，INTERVAL expr type) | 在date上减去一个时间                              |
| DATEDIFF(date1, date2)             | 返回起始时间date1和结束时间date2之间的天数        |
| TIMEDIFF(date1,date2)              | 两个时间差(多少小时多少分钟多少秒)                |
| NOW()                              | 返回当前日期和时间                                |
| YEAR(date)                         | 获取指定date的年份                                |
| MONTH(date)                        | 获取指定date的月份                                |
| DAY(date)                          | 获取指定date的日期                                |
| unix_timestamp()                   | 返回的是1970-1-1到现在的秒数                      |
| FROM_UNIXTIME()                    | 可以把一个 秒数[时间戳],转换成指定格式的日期      |

上面函数的细节说明:

- DATE ADD()中的interval 后面可以是 year minute second day等

- DATE SUB()中的interval 后面可以是 year minute second hour day等

- DATEDIFF(date1,date2) 得到的是天数，而且是date1-date2 的天数，因此可以取负数

- 这四个函数(还有一个Date)的日期类型可以是date, datetime或者timestamp

```sql
-- 日期时间相关函数
-- CURRENT_DATE() 当前日期 括号可以有也可以没有
SELECT CURRENT_DATE FROM DUAL #2023-02-04

-- CURRENT_TIME() 当前时间
SELECT CURRENT_TIME FROM DUAL #12:03:19

-- CURRENT_TIMESTAMP()当前时间戳
SELECT CURRENT_TIMESTAMP FROM DUAL #2023-02-04 12:03:57

-- DATE(datetime)返回datetime的日期部分
SELECT DATE(CURRENT_TIMESTAMP) FROM DUAL #2023-02-04

-- 在date2加上日期或者时间
#在当前日期加上了10天
SELECT DATE_ADD(CURRENT_DATE,INTERVAL 10 DAY) FROM DUAL #2023-02-14

-- 在date2减去日期或者时间
#在当前时间减去10分钟
SELECT DATE_SUB(CURRENT_TIME,INTERVAL 10 MINUTE) FROM DUAL #12:02:45

-- DATEDIFF(date1,date2)两个日期相差的天数
#是date1减去date2得到结果
SELECT DATEDIFF(CURRENT_DATE,DATE_ADD(CURRENT_DATE,INTERVAL 10 DAY)) FROM DUAL #-10

-- 两个时间差,精确到时分秒
SELECT TIMEDIFF(CURRENT_TIME,DATE_SUB(CURRENT_TIME,INTERVAL 10 MINUTE)) FROM DUAL #00:10:00

-- now() 当前时间
SELECT NOW() FROM DUAL #2023-02-04 12:18:11

-- YEAR MONTH DATE(DATETIME) 获取年月日
#获取NOW()的月份
SELECT MONTH (NOW()) FROM DUAL

 -- 如果你能活80岁，求出你还能活多少天. [练习]
#先求出活到80岁是什么日期
SELECT DATE_ADD('1999-8-8',INTERVAL 80 YEAR) FROM DUAL
#再用80岁的日期减去现在的日期就是还能活的天数
SELECT DATEDIFF(DATE_ADD('1999-8-8',INTERVAL 80 YEAR),NOW()) FROM DUAL #20639

-- unix_timestamp() : 返回的是1970-1-1到现在的秒数
SELECT UNIX_TIMESTAMP() FROM DUAL; #1675524073

-- FROM_UNIXTIME() :可以把一个 秒数[时间戳],转换成指定格式的日期
#细节:指定格式为 %Y-%m-%d %H:%m:%s ,这里就是年月日时分秒的格式
#意义:在开发中,可以存放一个整数,然后表示时间,通过它来转换
SELECT FROM_UNIXTIME(1675524073) FROM DUAL #2023-02-04 23:21:13
```

### 加密和系统函数

| 函数                                  | 功能                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| USER()                                | 查询用户                                                     |
| DATABASE()                            | 查询当前数据库名称                                           |
| MD5(str)                              | 为字符串算出一个MD5，32位的字符串，可用于密码加密            |
| PASSWORD(str)select * from mysql.user | 从原文密码str计算并返回密码字符串，通常用于对mysqI数据库的用户密码加密 |

```sql
-- 加密函数
-- USER()查询用户
SELECT USER() FROM DUAL #root@localhost

-- DATABASE () 当前数据库名称
SELECT DATABASE() FROM DUAL #yjm_db02

-- MD5 (str)为字符串算出一个MD5 32的字符串，(用户密码)加密
SELECT MD5('123456') FROM DUAL #e10adc3949ba59abbe56e057f20f883e
SELECT LENGTH(MD5('123456')) FROM DUAL # MD5之后的密码长度为32位
-- PASSWORD (str)  加密函数
#细节:MYSql默认使用PASSWORD (str) 函数对数据库用户进行加密
SELECT PASSWORD('123456') FROM DUAL #*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9

-- select * from mysql.user \G从原文密码str计算并返回密码字符串,
-- 通常用于对mysql数据用户密码进行加密
SELECT * FROM mysql.user #*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9
```

### 流程函数

| 函数                                                         | 功能                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------- |
| IF(value, t, f)                                              | 如果value为true，则返回t，否则返回f                     |
| IFNULL(value1, value2)                                       | 如果value1不为空，返回value1，否则返回value2            |
| CASE WHEN [ val1 ] THEN [ res1 ] … ELSE [ default ] END      | 如果val1为true，返回res1，… 否则返回default默认值       |
| CASE [ expr ] WHEN [ val1 ] THEN [ res1 ] … ELSE [ default ] END | 如果expr的值等于val1，返回res1，… 否则返回default默认值 |

```sql
-- IF(expr1,expr2,expr3)如果expr1为True ,则返回expr2否则返回expr3
SELECT IF(NULL,'真','假') FROM DUAL #假

-- IFNULL(expr1,expr2)如果expr1不为空NULL,则返回expr1,否则返回expr2
SELECT IFNULL('有数据','没数据') FROM DUAL #有数据
SELECT IFNULL(NULL,'没数据') FROM DUAL #没数据

-- SELECT CASE WHEN expr1 THEN expr2 WHEN expr3 THEN expr4 ELSE expr5 END; [类似多重分支.]
-- 如果expr1为TRUE,则返回expr2,如果expr3 为t,返回expr4,否则返回expr5
# 写法一
SELECT ename,(CASE
              WHEN job='CLERK' THEN
              '职员'
              WHEN job='MANAGER' THEN
              '经理'
              WHEN job='SALESMAN' THEN
              '销售人员'	
              ELSE
              job
              END) AS 'job'
FROM `emp`
# 写法二 写法二更直观，贴近java中的switch
SELECT ename,(CASE job
              WHEN 'CLERK' THEN
              '职员'
              WHEN 'MANAGER' THEN
              '经理'
              WHEN 'SALESMAN' THEN
              '销售人员'	
              ELSE
              job
              END) AS 'job'
 FROM `emp`
```

