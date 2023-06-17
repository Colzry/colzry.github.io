---
title: "MySQL入门"
description: "MySQL入门"
keywords: "MySQL"

date: 2023-06-16T16:41:30+08:00
lastmod: 2023-06-16T16:41:30+08:00

categories:
  - MySQL
tags:
  - MySQL
---

## 1. MySQL的介绍
### 1.1 MySQL的背景
前身属于瑞典的一家公司，MySQL AB
08年被sun公司收购
09年sun被oracle收购
### 1.2 MySQL的优点
1、开源、免费、成本低
2、性能高、移植性也好
3、体积小，便于安装

## 2. 准备操作
[win10安装](https://blog.csdn.net/qq_45740348/article/details/113705480)
[Linux安装](https://blog.csdn.net/qq_45740348/article/details/113705563)
打开管理员控制台
```
mysql -u root -p
```
查看数据库
```
show databases;
```
选择要操作的数据库
```
use 库名;
```
看看该库里面的表
```
show tables;
```
查看某个表的结构
```
desc 表名;
```

## 3. 查询语言的分类
在了解 SQL 之前我们需要知道下面这几个概念

-  数据定义语言：简称`DDL` (Data Definition Language)，用来定义数据库对象:数据库、表、列等； 
-  数据操作语言：简称`DML` (Data Manipulation Language)，用来对数据库中表的记录进行更新。关键字：insert、update、delete等 
-  数据控制语言：简称`DCL`(Data Control Language)，用来定义数据库访问权限和安全级别，创建用户等。关键字：grant等 
-  数据查询语言：简称`DQL`(Data Query Language)，用来查询数据库中表的记录，关键字：select from where等 

## 4. DDL语言

### 4.1 库的管理

注：方括号内的为选填内容【选填内容】
#### 4.1.1 创建数据库
```
create database 【if not exists】 库名;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232402.png#crop=0&crop=0&crop=1&crop=1&id=PflXi&originHeight=59&originWidth=781&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

#### 4.1.2 修改数据库
```
alter database 库名 character set 字符集名;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232403.png#crop=0&crop=0&crop=1&crop=1&id=pDCF9&originHeight=53&originWidth=701&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
##### 查看MYSQL数据库服务器和数据库字符集
```
show variables like '%character%';
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232404.png#crop=0&crop=0&crop=1&crop=1&id=JuIyS&originHeight=266&originWidth=786&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
##### 查看MYSQL所支持的字符集
```
show charset;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232405.png#crop=0&crop=0&crop=1&crop=1&id=t911O&originHeight=800&originWidth=671&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
#### 4.1.3 删除数据库
```
drop database 【if exists】 库名;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232406.png#crop=0&crop=0&crop=1&crop=1&id=gj7A8&originHeight=543&originWidth=421&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
### 4.2 表的管理
在这之前先了解一下**字段类型**和**约束**
#### 4.2.1 字段类型
##### 整型
| 类型名称 | 所占字节大小 |
| --- | --- |
| tinyint | 1 byte |
| smallint | 2 byte |
| mediumint | 3 byte |
| int/integer | 4 byte |
| bigint | 8 byte |

特点：
①都可以设置无符号和有符号，默认有符号，通过unsigned设置无符号
②如果超出了范围，会报out or range异常，插入临界值
③长度可以不指定，默认会有一个长度（长度代表显示的最大宽度，如果不够则左边用0填充，但需要搭配zerofill，并且默认变为无符号整型）

##### 浮点型
定点数：decimal(M,D)
浮点数:
float(M,D)	4 byte
double(M,D)	8 byte
特点：
①M代表整数部位+小数部位的个数，D代表小数部位
②如果超出范围，则报out or range异常，并且插入临界值
③M和D都可以省略，但对于定点数，M默认为10，D默认为0
④如果精度要求较高，则优先考虑使用定点数

##### 字符型
char：固定长度的字符，写法为char(M)，最大长度不能超过M，**其中M可以省略**，默认为1
varchar：可变长度的字符，写法为varchar(M)，最大长度不能超过M，**其中M不可以省略**

##### 日期型
year 年
date 日期
time 时间
datetime 日期+时间	8 byte
timestamp 日期+时间	4 byte   比较容易受时区、语法模式、版本的影响，更能反映当前时区的真实时间

#### 4.2.2 约束
##### 常见的约束
NOT NULL：非空，该字段的值必填
UNIQUE：唯一，该字段的值不可重复
DEFAULT：默认，该字段的值不用手动插入有默认值
PRIMARY KEY：主键，该字段的值不可重复并且非空  unique+not null
FOREIGN KEY：外键，该字段的值引用了另外的表的字段

主键和唯一

1. 区别：
① 一个表至多有一个主键，但可以有多个唯一
② 主键不允许为空，唯一可以为空
2. 相同点：
都具有唯一性
都支持组合键，但不推荐

#### 4.2.3 创建表

```
create table 【if not exists】表名(
	字段名 字段类型 【约束】,
	字段名 字段类型 【约束】,
	......
	字段名 字段类型 【约束】 
);
```
```
create table 表名(
	字段名 字段类型 not null, #非空
	字段名 字段类型 primary key, #主键
	字段名 字段类型 unique, #唯一
	字段名 字段类型 default 值, #默认
	字段名 enum('值1','值2','值3'...) not null default '值1'
	constraint 约束名 foreign key(字段名) references 主表(被引用列)
);
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232407.png#crop=0&crop=0&crop=1&crop=1&id=Dp2Pf&originHeight=187&originWidth=393&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210407181936.png#crop=0&crop=0&crop=1&crop=1&id=ID5SW&originHeight=257&originWidth=858&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

创建表时设置自增长列
```
create table 表(
	字段名 字段类型 约束 auto_increment
)
```

#### 4.2.4 修改表
```
alter table 表名 add|drop|modify|change column 列名 【列类型 约束】;
```
```
# 1. 添加列
alter table 表名 add column 列名 类型 【first|after 字段名】;
# 2. 修改列的类型或约束
alter table 表名 modify column 列名 新类型 【新约束】;
# 3. 修改列名
alter table 表名 change column 旧列名 新列名 类型;
# 4. 删除列
alter table 表名 drop column 列名;
# 5. 修改表名
alter table 表名 rename 【to】 新表名;
# 6.设定必须值
alter table 表名 modify column 字段 enum('教授','副教授','讲师','助教') not null default '教授';
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232408.png#crop=0&crop=0&crop=1&crop=1&id=YDd5Z&originHeight=67&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232409.png#crop=0&crop=0&crop=1&crop=1&id=ZFYT8&originHeight=76&originWidth=509&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232410.png#crop=0&crop=0&crop=1&crop=1&id=lDlbY&originHeight=77&originWidth=641&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210407185037.png#crop=0&crop=0&crop=1&crop=1&id=rE2Fl&originHeight=74&originWidth=1359&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
修改表时设置自增长列
```
alter table 表 modify column 字段名 字段类型 约束 auto_increment;
```
#### 4.2.5 删除表
```
drop table 【if exists】 表名;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232411.png#crop=0&crop=0&crop=1&crop=1&id=ZkSON&originHeight=363&originWidth=404&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
删除自增长列
```
alter table 表 modify column 字段名 字段类型 约束;
```
#### 4.2.6 复制表
```
# 1. 复制表的结构
create table 表名 like 旧表;
# 2. 复制表的结构+数据
create table 表名;
select 查询列表 from 旧表【where 筛选】;
```
## 5. DQL语言
### 5.1 基础查询
```
1、查询单个字段
select 字段名 from 表名;
2、查询多个字段
select 字段名，字段名 from 表名;
3、查询所有字段
select * from 表名
4、查询常量
select 常量值;
注意：字符型和日期型的常量值必须用单引号引起来，数值型不需要
5、查询函数
select 函数名(实参列表);
6、查询表达式
select 100/1234;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232412.png#crop=0&crop=0&crop=1&crop=1&id=fnkDy&originHeight=210&originWidth=601&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 5.2 条件查询
```
select 查询列表 from 表名 where 筛选条件;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232413.png#crop=0&crop=0&crop=1&crop=1&id=HUleB&originHeight=175&originWidth=588&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
#### 筛选条件的分类

1. 简单条件运算符
> < = <> != >= <=  <=>安全等于
2. 逻辑运算符
&& and
|| or
!  not
### 5.3 排序查询
```
select 查询列表 from 表 where 筛选条件 order by 排序列表 【asc | desc】;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232414.png#crop=0&crop=0&crop=1&crop=1&id=GU99e&originHeight=192&originWidth=661&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
说明：

1.  `asc` ：升序，如果不写默认升序
`desc`：降序 
2.  `order by` 的位置一般放在查询语句的最后（limit语句除外） 
### 5.4 分页查询
对于排序后的字段，或者不排序的字段，如果只希望显示一部分的话，就会使用 `LIMIT` 关键字来实现，比如我们只想取前三条记录
```
select * from 表名 limit 3;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232415.png#crop=0&crop=0&crop=1&crop=1&id=rd0r8&originHeight=176&originWidth=598&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
或者我们对排序后的字段取前三条记录
```
select * from 表名 order by 排序列表 desc limit 3;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232416.png#crop=0&crop=0&crop=1&crop=1&id=oHaTI&originHeight=181&originWidth=591&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
上面这种 limit 是从表记录的第 0 条开始取，如果从指定记录开始取，比如从第二条开始取，取三条记录
```
select * from 表名 order by 排序列表 desc limit 2,3;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232417.png#crop=0&crop=0&crop=1&crop=1&id=B8Zob&originHeight=179&originWidth=585&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
注意：limit 是 MySQL 扩展 SQL92 之后的语法，在其他数据库比如 Oracle 上就不通用

### 5.5 分组查询
```
select 分组函数，分组后的字段
from 表
【where 筛选条件】
group by 分组的字段
【having 分组后的筛选】
【order by 排序列表】;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232418.png#crop=0&crop=0&crop=1&crop=1&id=Ijl64&originHeight=353&originWidth=592&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)
### 5.6 聚合查询

下面我们来看一下对记录进行汇总的操作，这类操作主要有

-  `汇总函数`，比如 sum 求和、count 统计数量、max 最大值、min 最小值等 
-  `group by`，关键字表示对分类聚合的字段进行分组，比如按照部门统计员工的数量，那么 group by 后面就应该跟上部门 
-  `with` 是可选的语法，它表示对汇总之后的记录进行再次汇总 
-  `having` 关键字表示对分类后的结果再进行条件的过滤。 

看起来 where 和 having 意思差不多，不过它们用法不一样，where 是使用在统计之前，对统计前的记录进行过滤，having 是用在统计之后，是对聚合之后的结果进行过滤。也就是where 永远用在 having 之前，我们应该先对筛选的记录进行过滤，然后再对分组的记录进行过滤。

可以对 employees表中员工薪水进行统计，选出总共的薪水、最大薪水、最小薪水

```
select sum(salary) from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232419.png#crop=0&crop=0&crop=1&crop=1&id=XBy9G&originHeight=141&originWidth=409&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
select max(salary),min(salary) from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232420.png#crop=0&crop=0&crop=1&crop=1&id=h4i4W&originHeight=142&originWidth=502&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

统计 employees表中人员的数量
```
# as 别名
select count(1) as Persons from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232421.png#crop=0&crop=0&crop=1&crop=1&id=bhlia&originHeight=140&originWidth=454&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 5.7 连接查询
```
select 表1的字段 
left|right|inner join 表2的字段 
on 连接条件;
```
内连接：选出两张表中互相匹配的记录
外连接：不仅选出匹配的记录，也会选出不匹配的记录
外连接分为两种

-  左外连接：筛选出包含左表的记录并且右表没有和它匹配的记录(以左表的内容为主，右表中没有的内容为空) 
-  右外连接：筛选出包含右表的记录并且左表没有和它匹配的记录(以右表的内容为主，左表中没有的内容为空) 

使用左外连接查询
```
select employees.name,departments.name from employees
left join departments on 
employees.department_id = departments.id;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232422.png#crop=0&crop=0&crop=1&crop=1&id=waJMb&originHeight=252&originWidth=552&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

使用右外连接查询
```
select employees.name,departments.name from employees 
right join departments on 
employees.department_id = departments.id;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232423.png#crop=0&crop=0&crop=1&crop=1&id=BPo18&originHeight=249&originWidth=558&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 5.8 子查询

- 嵌套在其他语句内部的`select`语句称为子查询或内查询，
- 外面的语句可以是`insert`、`update`、`delete`、`select`等，一般`select`作为外面语句较多
- 外面如果为`select`语句，则此语句称为外查询或主查询

子查询的一些关键字 **「in、not in、=、!=、exists、not exists」** 等
```
# 最低工资
select min(salary) from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232424.png#crop=0&crop=0&crop=1&crop=1&id=YwlQr&originHeight=115&originWidth=390&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
# 查询员工的姓名和工资，要求工资=最低工资
select name,salary
from employees
where salary=(
	select min(salary) from employees
);
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232425.png#crop=0&crop=0&crop=1&crop=1&id=Kwybt&originHeight=214&originWidth=381&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
# 查询出每个人的工作类型，没有的则不显示
select * 
from employees
where department_id in (
	select id from departments
);
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232426.png#crop=0&crop=0&crop=1&crop=1&id=tpvSq&originHeight=270&originWidth=595&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 5.9 联合查询

我们还经常会遇到这样的场景，将两个表的数据单独查询出来之后，将结果合并到一起进行显示，这个时候就需要 UNION 和 UNION ALL 这两个关键字来实现这样的功能，UNION 和 UNION ALL 的主要区别是 UNION ALL 是把结果集直接合并在一起，而 UNION 是将 UNION ALL 后的结果进行一次 `DISTINCT` 去除掉重复数据。
```
select gender from employees union all select department_id from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232427.png#crop=0&crop=0&crop=1&crop=1&id=HPAgP&originHeight=310&originWidth=745&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
select gender from employees union select department_id from employees;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232428.png#crop=0&crop=0&crop=1&crop=1&id=jsZ0R&originHeight=234&originWidth=729&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

## 6. DML语言
### 6.1 插入
```
insert into 表名 values(值,...);
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232429.png#crop=0&crop=0&crop=1&crop=1&id=GmnC0&originHeight=64&originWidth=737&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

假如表中有可以为null的字段，注意可以通过以下两种方式插入null值
```
insert into 表名(字段名,...) values(值,...);
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232430.png#crop=0&crop=0&crop=1&crop=1&id=TGaT6&originHeight=302&originWidth=691&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

```
insert into 表名 set 字段=值,字段=值,...;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232431.png#crop=0&crop=0&crop=1&crop=1&id=R0ZeE&originHeight=301&originWidth=631&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

### 6.2 修改

修改单表的记录
```
update 表名 set 字段=值,字段=值 【where 筛选条件】;
```

```
# 将Mike的薪水更改为12000
update employees set salary=12000 where name='Mike';
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232432.png#crop=0&crop=0&crop=1&crop=1&id=TNTbM&originHeight=286&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

修改多表的记录
```
update 表1 别名 
left|right|inner join 表2 别名 
on 连接条件  
set 字段=值,字段=值 
【where 筛选条件】;
```

### 6.3 删除
法一：使用`delete`
删除单表的记录
```
delete from 表名 【where 筛选条件】;
```
![](https://picture-czy.oss-cn-beijing.aliyuncs.com/img/20210208232433.png#crop=0&crop=0&crop=1&crop=1&id=S3oww&originHeight=279&originWidth=603&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

删除多表的记录
```
delete 别名1,别名2 from 表1 别名 
inner|left|right join 表2 别名 
on 连接条件
【where 筛选条件】;
```

法二：使用`truncate`
```
truncate table 表名;
```

**两种方式的区别：**

-  `truncate`删除后，如果再插入，标识列从1开始
`delete`删除后，如果再插入，标识列从断点开始 
-  `delete`可以添加筛选条件
`truncate`不可以添加筛选条件 
-  `truncate`效率较高 
-  `truncate`没有返回值
`delete`可以返回受影响的行数 
-  `truncate`不可以回滚
`delete`可以回滚 

## 7. TCL语言
### 7.1 事务
#### 一、含义
事务：一条或多条sql语句组成一个执行单位，一组sql语句要么都执行要么都不执行

#### 二、特点（ACID）
A 原子性：一个事务是不可再分割的整体，要么都执行要么都不执行
C 一致性：一个事务可以使数据从一个一致状态切换到另外一个一致的状态
I  隔离性：一个事务不受其他事务的干扰，多个事务互相隔离的
D 持久性：一个事务一旦提交了，则永久的持久化到本地
#### 三、事务的使用步骤
开启事务
```
set autocommit=0;
start transaction; #可以省略
```

设置回滚点
```
savepoint 回滚点名;
```

结束事务
```
commit; #提交
rollback; #回滚
rollback to 回滚点名; #回滚到指定的地方
```

设置事务隔离级别
> `read uncommitted`:读未提交
`read committed`：读已提交
`repeatable read`：可重复读
`serializable`：串行化

```
set transaction isolation level read committed;
```
