# 第一章 数据库和SQL概述

## 可保存数据的容器

> + 集合【内存层面保存数据，断电即失】
> + 数组【内存层面保存数据，断电即失】
> + 文件：【可实现数据持久化，但不利于查找】
> + 数据库软件【可实现数据持久化，且使用完整的管理系统统一管理，结构化查询】

## 数据库的相关概念

> + DB：数据库（database），即存储数据的”仓库“，它保存了一系列有组织的数据
>
> + DBMS：数据库管理系统（Database Management System）简称数据库软件或数据库产品
>   + 数据库（DB）是通过数据库管理系统（DBMS）创建和操作的容器（即DBMS用于管理DB中的数据）
>   + 常见的数据库软件（MySQL、Oracle、DB2、SqlServer等）
> + SQL：结构化查询语言（Structure Query Language），是专门用于与数据库通信的语言

## 数据库的特点

> + 将数据放到表中，表再放到库中
> + 一个数据库中可以有多张表，每张表都有一个名字，用来标识自己【表名具有唯一性】
> + 表具有一些特性（属性），这些特性定义了数据在表中如何存储，类似Java中“类的设计”
> + 表由列组成，我们也称为字段。所有表都是由一个或多个列组成，每一列类似Java中的属性
> + 表中的数据是按行存储的，每一行类似于Java中的一个”对象“

## SQL的优点

> + SQL语言不是某个特定数据库供应商专有的语言，几乎所有DBMS（主流数据库软件）都支持SQL语言
> + SQL语言简单易学
> + SQL语言虽然简单，但实际上是一种强有力的语言，灵活使用其语言元素，可以进行非常复杂和高级的数据库操作

# 第二章 MySQL数据库的安装与使用

## MySQL数据库管理系统的介绍

> MySQL数据库隶属于MySQL AB公司，总部位于瑞典，后被oracle收购。

## MySQL产品的优点

> + 成本低：开放源代码，一般可以免费试用
> + 性能高：执行很快，移植性好
> + 简单：体积小，便于安装和使用

## MySql数据库的安装

> + 安装MySQL环境
>   + MySQL数据库的下载地址：[MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)（或者网盘获取MySQL 5.5.msi）
>   + 接受协议→下一步选择Custom自定义安装→选择安装路径→下一步安装！
>   + 弹出广告一路next→Finish【之后会弹出配置窗口，继续往下⬇（若不慎关闭配置窗口，则在安装路径E:\Develop\MySQL\MySQL Server 5.5\bin下运行MySQLInstanceConfig.exe就可以再次打开该窗口）】
> + 配置MySQL
>   + 下一步→选择默认的**Detailed Configuration**精确配置→下一步选择默认的**Developer Machine**开发机→下一步选择多功能数据库**Multifunctional Database**→下一步→还是选择第一个**Decision Support**→下一步→选择字符集**选第三个Manual...**设置为**utf8**→下一步选中**Include**选项，意为将MySQL的bin目录添加进环境变量（当然也可以指定Service Name，即MySQL的服务名称）→下一步设置**root管理员密码**，设置完后勾选**Enable root..**允许远程机访问
>   + Execute就完毕了

> DBMS分为两类：
>
> + 基于共享文件系统的DBMS （Access ）
> + 基于客户机——服务器的DBMS（MySQL、Oracle、SqlServer），安装数据库，MySQL属于C/S架构，但主要是安装数据库的服务端

## MySQL数据库的卸载

> + 使用系统的应用与功能搜索到MySQL Server 5.5，点击进行卸载
>
> + 其后需要对残留的文件夹等文件进行删除
>   + 如MySQL安装路径下的文件夹会残留my.ini配置文件
>   + 隐藏的项目中programData文件夹里的MySQL一并删除即可

## MySQL配置文件的介绍

> 若修改了配置文件中的配置，需要重启MySQL服务方可生效

```ini
# 对应服务端的MySQL
[mysqld]
# The TCP/IP Port the MySQL Server will listen on
# 服务端端口号，开发中根据实际需要可修改
port=3306

#Path to installation directory. All paths are usually resolved relative to this.
#MySQL的安装目录
basedir="E:/Develop/MySQL/MySQL Server 5.5/"

#Path to the database root
#MySQL的数据文件存储目录
datadir="C:/ProgramData/MySQL/MySQL Server 5.5/Data/"

# The default character set that will be used when a new schema or table is
# created and no character set is defined
#服务端字符集，开发中根据实际需要可修改
character-set-server=utf8

# The default storage engine that will be used when create new tables when
#存储引擎，用于执行SQL语句，开发中根据实际需要可修改
default-storage-engine=INNODB
```

## MySQL服务的启动与停止

> MySQL服务的启动和停止的方式：
>
> + 方式一：开始→计算机管理→服务和应用程序→服务→MySQL→可启动也可停止（若不经常使用MySQL可设置为手动，重新开机时就不会自动启动了，这样节省资源消耗，若需要经常使用，则设置为自动）
> + 方式二（推荐）：管理员身份运行DOS窗口
>   + 开启MySQL服务：net start MySQL（注意要与安装时自定义的MySQL服务名称相对应，默认就是MySQL）
>   + 关闭MySQL服务：net stop MySQL

## MySQL服务端的登录和退出^命令行方式^

> 登录MySQL服务端的前提：必须保证MySQL服务是启动的，否则即使有权限也无法登录（废话）
>
> + MySQL登录和退出的方式一：可以通过MySQL自带的命令行客户端（MySQL 5.5 Command Line Client）进行登录【缺点就是仅限root用户，其他用户无法使用】
>   + 登录：直接输入root用户的密码即可登录成功
>   + 退出：exit或Ctrl + c即可关闭MySQL命令行窗口
> + MySQL登录和退出的方式二：通过Windows自带的命令行窗口进行登录，具体操作如下：

```shell
# -h 为host的缩写，指明MySQL要连接的主机名
# -P 为Port的缩写，指明该MySQL程序对应的端口号
# -u 为user的缩写，指明登录该MySQL的用户名
# -p 为password的缩写，指明该MySQL用户所对应的密码
C:\Users\Administrator>mysql -h localhost -P 3306 -u root -p
Enter password: ******

#注意点:-p后直接写密码可以显示密码，回车直接登录成功，但-p与密码之间不能存在空格
C:\Users\Administrator>mysql -h localhost -P 3306 -u root -p123456

#当然，若只连接本机上的MySQL且端口号为3306的，主机名与端口号可以省略不写
C:\Users\Administrator>mysql -u root -p
Enter password: ******
```

## MySQL环境变量的配置

> 其实在安装步骤中，勾选了Include选项，那么它将自动将环境变量配置好，但只会配置在path环境变量的最后方，避免不了从头检测时，遇到其它MySQL文件的路径，造成登录异常，那么我们就需要手动配置，具体步骤如下：
>
> + 复制包含bin目录在内的MySQL的安装路径，如：E:\Develop\MySQL\MySQL Server 5.5\bin
> + 为了保险起见，将此路径粘贴在path中的首行即可（若是用命令行登录MySQL则别忘了重启命令行窗口）

## MySQL常见的命令介绍

> 每条SQL语句的结尾可使用分号;或\g结尾，但肯定推荐使用分号结尾啊

|                             命令                             |                           命令描述                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                       show databases;                        |              查看当前MySQL服务端中所有的数据库               |
|                      use + 数据库名称;                       |                       打开指定的数据库                       |
|                         show tables;                         |                   查看当前数据库中的所有表                   |
|                show tables from + 数据库名称;                |                 直接查看其它数据库中的所有表                 |
| create tabe + 表名(<br />字段名1  字段类型1,<br />字段名2  字段类型2,<br />...); | 创建表结构<br />多个字段之间使用逗号隔开<br />最后一个字段不用逗号结尾 |
|                         desc + 表名;                         |                          查看表结构                          |
|                      select version();                       |                  查看当前MySQL服务端的版本                   |

```shell
# 登录MySQL数据库管理系统
C:\Users\Administrator>mysql -u root -p
Enter password: ******

# show databases;【显示都有哪些数据库】
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
#前三个不要去动即可
| information_schema：保存元数据 |
| mysql：保存用户信息              |
| performance_schema：搜集性能信息/参数|
#测试数据库
| test               |
+--------------------+

# 打开test数据库
mysql> use test;
Database changed #已进入

# 展示这个数据库中的所有表
mysql> show tables;
Empty set (0.00 sec) #空的

# 直接展示mysql这个数据库中的表
mysql> show tables from mysql;
#...

# 查看当前所处的数据库，显然当前还存在test库中
mysql> select database();
+------------+
| database() |
+------------+
| test       |
+------------+
1 row in set (0.00 sec)

# 创建一个myTable表，具有id,name字段
mysql> create table myTable(
    -> id int,
    -> name varchar(20)
    -> );
Query OK, 0 rows affected (0.01 sec)

# 查看myTable的表结构
mysql> desc myTable;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(20) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.00 sec)

# 通过查询数据，查看表中是否存在数据，很显然是空表
mysql> select * from myTable;
Empty set (0.00 sec)

# 向myTable表中插入两条数据
mysql> insert into myTable(id,name) values(1001,'Martha');
Query OK, 1 row affected (0.01 sec)

mysql> insert into myTable(id,name) values(1002,'Beth');
Query OK, 1 row affected (0.01 sec)

# 再次查看myTable表中的数据
mysql> select * from myTable;
+------+--------+
| id   | name   |
+------+--------+
| 1001 | Martha |
| 1002 | Beth   |
+------+--------+
2 rows in set (0.00 sec)

# 修改myTable表中id为1001对应的name字段值为Maserati
mysql> update myTable set name = 'Maserati' where id = 1001;
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

# 再次查看更改后的表中数据
mysql> select * from myTable
+------+----------+
| id   | name     |
+------+----------+
| 1001 | Maserati |
| 1002 | Beth     |
+------+----------+
2 rows in set (0.00 sec)

# 删除myTable表中id为1002的数据
mysql> delete from myTable where id = 1002;
Query OK, 1 row affected (0.01 sec)

# 再次查看删除后的表中数据
mysql> select * from myTable;
+------+----------+
| id   | name     |
+------+----------+
| 1001 | Maserati |
+------+----------+
1 row in set (0.00 sec)

# 查看当前MySQL服务端的版本
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.5.20    |
+-----------+
1 row in set (0.00 sec)

# 在Windows命令行窗口查看MySQL服务端版本的两种方式
C:\Users\Administrator>mysql -V
mysql  Ver 14.14 Distrib 5.5.20, for Win64 (x86)

C:\Users\Administrator>mysql --version
mysql  Ver 14.14 Distrib 5.5.20, for Win64 (x86)
```

## MySQL的语法规范

> + 不区分大小写，但建议关键字大写，其他的（如表名、列名）小写【图形化界面的会自动规范，命令行方式需要手动规范】
> + 每条命令最好使用分号结尾
> + 每条语句可根据需要，进行缩进或换行
> + 注释
>   + 单行注释：#注释文字
>   + 单行注释：-- 注释文字
>   + 多行注释：/*  注释文字  */
> + 细节补充：
>
>   + 对指定表的操作最好进入该表所处的数据库（use xxx进入数据库进行操作）
>
>   + 选中要执行的SQL语句→F9执行语句
>   + 选中要执行的SQL语句→F12进行MySQL规范格式化
>   + ``这个符号是可以区分关键字与字段名的，比如区分（关键字NAME与字段名name就要使用该符号）

## 图形化界面客户端程序的安装

> 网盘获取（SQLyog Ultimate - 64 bit.exe）进行傻瓜式安装

# 第三章 SQL语言

## DQL数据查询语言

> DQL（Data Query Language）数据查询语言

### 基础查询

>查询的基本语法：select 查询列表 from 表名;
>
>查询列表的特点
>
>+ 查询列表可以是表中的字段、常量值、表达式、函数，也可以是前四者的组合
>+ 查询的结果是一个虚拟的表格（即虚表）

```mysql
-- 查询表中字段的操作
# 查询employees表中的单个字段lastname;
SELECT last_name FROM employees;

# 查询表中的多个字段first_name,salary,email;
SELECT first_name,salary,email FROM employees;

# 查询表中的所有字段
# 方式一：可自主设置所查询出来的字段顺序
SELECT
  `first_name`,
  `last_name`,
  `email`,
  `phone_number`,
  `job_id`,
  `salary`,
  `commission_pct`,
  `manager_id`,
  `department_id`,
  `hiredate`
FROM
  employees;
  
# 方式二：查询出来的字段顺序是固定的
SELECT * FROM employees;
--------------------------------------------------------------------------------------------------------
-- 查询表中常量值、表达式、函数的操作
# 查询常量值：SQL中字符与字符串一律使用''单引号
# 字符型与日期型的常量值必须使用单引号引起
SELECT 100, 'jack', TRUE, FALSE, 'true', 'false', 88.66, 'h', "hello", "w";

# 查询表达式
SELECT 3 > 5, 6 > 2, 100 * 5, 100 % 30, 20 + 6, 3 * 8,TRUE, FALSE, 'TRUE', 'false';

# 查询函数
SELECT NOW(), VERSION(), CURRENT_TIMESTAMP(), LOCALTIME(), LOCALTIMESTAMP();
```

#### 起别名

> 为字段起别名：
>
> + 更便于理解，提高可读性
> + 若查询的字段出现重名的情况（查询不同表的字段时可能会出现），使用别名可区分开来

```mysql
# 方式一：使用AS关键字起别名
SELECT 11-3 AS 减法的结果;
SELECT last_name AS 姓,first_name AS 名 FROM employees;

# 方式二：省略AS关键字直接别名
SELECT last_name 姓,first_name 名 FROM employees;

# 注意：若别名中含有空格，为避免其中与关键字重名，建议使用双引号引起
SELECT salary "out put" FROM employees;
```

#### 去重复

```mysql
# SELECT + DISTINCT关键字用于查找出单个字段并去重复值
# 注意：去重复只允许作用在单个字段上，因为不一定多个字段去重后的行数都相同
SELECT DISTINCT department_id FROM employees;
```

#### +号的作用

> + Java中的+号作用
>   + 运算符：两个操作对象都为数值型数据
>   + 连接符：两个操作对象有一方为字符串型数据
>
> + MySQL中+号作用
>   + 仅为运算符
>     + SELECT 100+90;【190：两个数据都为数值型则直接做加法运算】
>     + SELECT '123' + 7;【130：两个数据有一方为字符型，则视图将字符型数据转换为数值型数据，转换成功则继续做加法运算】
>     + SELECT 'Martha' + 90;【90：转换失败则直接将字符型数据转换为数值0，再做加法运算】
>     + SELECT null + 10;【null：若有一方数据为null值，那么直接返回null，null和任何数据拼接都返回null】

#### ifnull()函数

> 判断某字段或表达式是否为null，如果为null则返回指定param2的值，否则返回原本param1的值
>
> 格式IFNULL(param1，param2)
>
> 补充：ISNULL(param)：判断某字段是否为null，是则返回1，否则返回0，1代表true，0代表false

#### concat()连接

> concat(str1,str2...)：可以有多个参数进行拼接

```mysql
# 拼接员工的名与姓字段中的数据
SELECT CONCAT(last_name,' ',first_name) 姓名 FROM employees;

# 拼接多个列的数据，使用逗号隔开【注意：其中某个列可能存在null值影响结果，此处就需要使用到函数IFNULL(param1,param2)】
SELECT CONCAT(`first_name`,',',`email`,IFNULL(`commission_pct`,0)) AS "OUT PUT" FROM employees;
# IFNULL()函数的参数一：指明可能出现null值的字段，参数二为：出现null值用啥替换
```

### 条件查询

> 条件查询的基本语法：select 查询列表 from 表名 where 筛选条件;
>
> 筛选条件分类：【】括起来的是MySQL自己的语法，建议使用MySQL定义的语法
>
> + 按条件表达式筛选
>   + 条件运算符：>	<	=	!=【<>】	>=	<=	<=>
>   + 注意：= 和 !=/<> 不能判断与null值是否相等或不相等
>   + <=>（安全等于）：可用于判断一切类型的数据是否相等，包括null值在内；但比较少用，原因是可读性差些
> + 按逻辑表达式筛选：逻辑表达式就是用于连接多个条件表达式的
>
>   + 逻辑运算符：&&	||	!【and	or	not】
> + 模糊查询
>
>   + like：一般都和通配符搭配使用
>
>     + %：代表任意多个字符，当然也包含0个字符
> + _：代表任意单个字符
>     + 可用于判断字符型数据和数值型数据，但like无法用于匹配null值
> + between..and
>     + 比起使用>=，<=，BETWEEN .. AND可提高语句的简洁度
>   + 包含临界值，但要保证两个临界值不要调换顺序（其原理就是>=左边值<=右边值，所以调换可能不出结果）
>   + in
>   + 用于判断某字段的值是否属于in列表中的某一项
>     + 比起使用OR，in可提高语句简洁度
>     + in列表中的值类型必须一致或者兼容（如'123',123），并且值不支持结合通配符使用
>   + is null
>     + is null：若一个字段的数据是null值，返回true
>     + is not null：若一个字段的数据不是null值，返回true
>
> 补充：IS NULL与<=>的对比
>
> + IS NULL：仅仅只能判断null值；可读性高；（建议使用）
> + <=>：既可以判断null值，也可以判断普通数值；可读性查差；

```mysql
-- 按条件表达式筛选
# 查询工资大于12000的员工信息
SELECT * FROM employees WHERE salary > 12000;
# 查询部门编号不等于90号的员工名和部门编号
SELECT CONCAT(last_name,' ',first_name) AS 员工名,department_id AS 部门编号 FROM employees WHERE department_id <> 90;
--------------------------------------------------------------------------------------------------------
-- 按逻辑表达式筛选
# 查询员工工资在10000-20000之间的员工名、工资以及奖金
 SELECT
  CONCAT (last_name, ' ', first_name) "员工名",
  salary "工资",
  salary * IFNULL (commission_pct, 1) "奖金",
  commission_pct
FROM
  employees
WHERE salary >= 10000
  AND salary <= 20000;
  
# 查询部门编号不是在90-110之间的，或者工资高于15000的员工信息
SELECT
  *
FROM
  employees
WHERE (
    department_id < 90
    OR department_id > 110
  )
  OR salary > 15000;
--------------------------------------------------------------------------------------------------------
-- 模糊查询
# 查询员工名中包含a字符的员工信息，不会区分大小写
SELECT * FROM employees WHERE last_name LIKE '%A%';

# 查询员工名称第三个字符为a，第五个字符为e的员工信息
SELECT * FROM employees WHERE last_name LIKE '__a_e%';

# 查询员工名称第二个字符为_的员工信息
# 方式一：使用\进行字符转义
SELECT * FROM employees WHERE last_name LIKE '_\_%';
# 方式二(MySQL推荐)：使用escape 指明转义字符（可指明任意字符为转义字符）
SELECT * FROM employees WHERE last_name LIKE '_$_%' ESCAPE '$';
# 查询员工编号为三位数且第一位字符为1的员工信息（虽然判断的是数值型，但还是要使用单引号引起所要判断的数据）
SELECT * FROM employees WHERE department_id LIKE '1__';

# 查询员工编号在100-120之间的员工信息
SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 120;
# 查询员工的工种编号满足AD_VP、AD_PRES、IT_PROT三者之一的员工名称与工种编号
SELECT CONCAT(last_name,' ',first_name) AS "姓名",job_id AS "工种编号"
FROM employees
WHERE job_id IN('AD_VP','IT_PROT','AD_PRES');
# 查询没有奖金的员工名和奖金率
SELECT last_name,commission_pct
FROM employees
WHERE commission_pct <=> NULL;
# 查询工资为12000的员工信息
SELECT * FROM employees WHERE salary <=> 12000;
```

### 排序查询

> 排序查询的基本语法：select 查询列表 from 表名 where 筛选条件 order by 排序列表 asc/desc
>
> 排序的特点
>
> + asc代表的是升序（低→高，小→大），desc代表的是降序（高→低，大→小）;若不显式地指定顺序，默认为asc升序排序
> + order by子句可以支持单个字段、多个字段、表达式、函数、别名
> + order by子句一般是放在查询语句的最后面，但limit子句除外，它是放在order by 子句的后面

```mysql
# 查询员工信息，要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC;

# 查询部门编号>=90的员工信息，要求按入职时间的先后进行排序
SELECT * FROM employees WHERE department_id >= 90 ORDER BY hiredate ASC;

# 按年薪的高低显示员工的信息和年薪【按表达式排序】
SELECT *,salary * 12 * (1 + IFNULL(commission_pct,0)) AS "年薪"
FROM employees 
ORDER BY salary * 12 * (1 + IFNULL(commission_pct,0)) DESC;

# 按年薪的高低显示员工的信息和年薪【按别名排序】
SELECT *,salary * 12 * (1 + IFNULL(commission_pct,0)) AS "年薪"
FROM employees 
ORDER BY 年薪 DESC;

# 按名称的长度升序显示员工的姓名和工资【按函数排序】
SELECT last_name,salary FROM employees ORDER BY LENGTH(last_name);

# 查询员工信息，要求先按工资排序，再按员工编号排序
SELECT * FROM employees ORDER BY salary ASC,employee_id ASC;
```

### 常见的函数

> 概念：类似于Java中的方法，将一组逻辑或者语句封装在方法体中，对外暴露方法名
>
> 好处：
>
> + 隐藏了实现细节
> + 提高了代码的重用性
>
> 调用语法：select 函数名(实参列表)  [from 表名];【若函数中的参数使用到了表中的字段，那么就加上from 表名，否则不用加】
>
> 函数的分类：
>
> + 单行函数：如（concat、length、ifnull等）
> + 分组函数：做统计使用，又称为统计函数、聚合函数、组函数

#### 单行函数

> 单行函数包括以下几种：
>
> + 字符函数
> + 数学函数
> + 日期函数
> + 其他函数【补充】
> + 流程控制函数【补充】

##### 字符函数

```mysql
# length()：获取参数值的字节个数（utf8字符集下，中文占3字节，英文占1字节）
SELECT LENGTH('Martha'); -- 5
SELECT LENGTH('顾森湘aaa'); -- 12

# concat()：拼接字符串
SELECT CONCAT(last_name,'_',first_name) AS 姓名 FROM employees;

# upper()：将字母转为大写
SELECT UPPER('john'); -- JOHN

# lower()：将字母转为小写
SELECT LOWER('Martha'); -- martha
# 将姓变大写名变小写，然后拼接【由此得出，函数可以嵌套调用】
SELECT CONCAT(UPPER(last_name),' ',LOWER(first_name)) AS 姓名 FROM employees;

# substr()：截取字符串【注意：在SQL中索引从1开始】
# 两参数：截取从指定索引处开始到结尾的所有字符
SELECT SUBSTR('你是不是我的菜',5) 结果; -- 我的菜

# 三参数：截取指定索引开始指定长度的字符
SELECT SUBSTR('你是不是我的菜',1,4) 结果; -- 你是不是
# 姓名中首字符大写，其他字符小写，使用_拼接，并显示
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),LOWER(SUBSTR(last_name,2)),'_',LOWER(first_name)) AS 姓名 FROM employees;
SELECT CONCAT(last_name,'_',first_name) FROM employees;

# instr()：返回子字符串第一次出现的索引位置，找不到则返回0
SELECT INSTR('我与玛莎','玛莎') AS result; --3

# trim()：去除前后空格
SELECT TRIM('   Martha  ') AS result;

# trim(param1 FROM param2)：去除param2前后指定的param1
SELECT TRIM('a' FROM 'aaaaaaaaaaaaaaaMarthaaaaaaaaaaaaaaa') AS result; -- Marth

# lpad()：用指定的字符进行左填充至指定的长度(若指定长度小于需要填充的字符串长度，则从该字符串右边截取掉长度以保证与指定长度相符)
SELECT LPAD('Martha',10,'*') AS result; -- ****Martha

# rpad()：用指定的字符进行右填充至指定的长度(若指定长度小于需要填充的字符串长度，则从该字符串左边截取掉长度以保证与指定长度相符)
SELECT RPAD('Martha',10,'*') AS result; -- Martha****

# replace(param1,param2,param3)：使用param2替换param1中所有匹配的字符串为param3
SELECT REPLACE('Martha love Beth','Beth','zx-10r') AS result; -- Martha love zx-10r
```

##### 数学函数

```mysql
# round()：四舍五入
SELECT ROUND(1.65) AS result; -- 2
# round(param1,param2)：将param1四舍五入并保留小数后param2位
SELECT ROUND(1.658,2) AS result; -- 1.66

# ceil()：向上取整，即返回≥该参数的最小整数
SELECT CEIL(1.001) AS result; -- 2

# floor()：向下取整，即返回≤该参数的最大整数
SELECT FLOOR(9.99) AS result; -- 9

# truncate()：截断，即保留几位小数
SELECT TRUNCATE(1.65414,1) AS result; -- 1.6

# mod()：取余数：相当于10%3
SELECT MOD(10,3) AS result; -- 1

# rand()：取随机数【1-99】
SELECT FLOOR(RAND() * 99 + 1);
```

##### 日期函数

| 格式符 |                      格式符描述                      |
| :----: | :--------------------------------------------------: |
|   %Y   |              代表四位年份<br />如1999年              |
|   %y   |               代表两位年份<br />如99年               |
|   %m   | 代表单位数补0的月份<br />如（01，02，03...10,11,12） |
|   %c   | 代表单位数不补0的月份<br />如（1，2，3...10,11,12）  |
|   %d   |     代表天数<br />如（01，02，03...28/29/30/31）     |
|   %H   |                     代表24小时制                     |
|   %h   |                     代表12小时制                     |
|   %i   |                       代表分钟                       |
|   %s   |                       代表秒钟                       |

```mysql
# now()：返回当前系统日期+时间
SELECT NOW() result; -- 2021-08-19 15:52:23

# curdate()：返回当前系统日期，不包含时间
SELECT CURDATE() result; -- 2021-08-19

# curtime()：返回当前系统时间，不包含日期
SELECT CURTIME() result; -- 15:55:37

# year、month、day、hour、minute、second()：获取指定日期时间中的年、月、日、时、分、秒
SELECT YEAR('2021-08-19 15:52:23') 年; -- 2021
SELECT MONTH('2021-08-19 15:52:23') 月; -- 8
SELECT DAY('2021-08-19 15:52:23') 日; -- 19
SELECT HOUR('2021-08-19 15:52:23') 时; -- 15
SELECT MINUTE('2021-08-19 15:52:23') 分; -- 52
SELECT SECOND('2021-08-19 15:52:23') 秒; -- 23

# monthname()：获取指定的日期时间中指定月份的英文名称
SELECT MONTHNAME('2021-08-19') result; -- August

# str_to_date()：将日期格式的字符串通过指定的格式转换为日期
SELECT STR_TO_DATE('2000-09-17','%Y-%m-%d'); -- 2000-09-17
# 查询入职日期为1992-4-3的员工信息
# 方式一：
SELECT * FROM employees WHERE hiredate = '1992-4-3';
# 方式二：考虑业务场景下，用户输入的状态
SELECT * FROM employees WHERE hiredate = STR_TO_DATE('4-3 1992','%m-%d %Y');

# date_format()：将日期转换为字符串
SELECT DATE_FORMAT(NOW(),'%Y年%m月%d日');
# 查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name,DATE_FORMAT(hiredate,'%m月/%d日 %Y年') FROM employees
WHERE commission_pct IS NOT NULL;
```

##### 其他函数

```mysql
# 查看当前数据库的版本号
SELECT VERSION();
# 查看当前的数据库
SELECT DATABASE();
# 查看当前用户
SELECT USER();
```

##### 流程控制函数

```mysql
# if(param1，param2，param3)：param1返回true执行param2，返回false执行param3
SELECT IF(10<5,'大','小'); -- 小

/* case函数语法一：类似于switch-case结构
	case：要判断的字段或者表达式
  when 常量1 then 要显示的值1或语句1;【是值就不要加分号否则报错】
  when 常量2 then 要显示的值2或语句2;【是值就不要加分号否则报错】
  ...
  else 要显示的值n或者语句n;【是值就不要加分号否则报错，else可省略】
  end
*/
# 查询员工的工资，要求如下
/*
	部门号=30，显示的工资为1.1倍
	部门号=40，显示的工资为1.2倍
	部门号=50，显示的工资为1.3倍
	其他部门，显示的为原工资
*/
SELECT salary,department_id,
CASE department_id
WHEN 30 THEN salary * 1.2
WHEN 40 THEN salary * 1.3
WHEN 50 THEN salary * 1.4
ELSE salary
END AS 新工资
FROM employees;
/* case函数语法二：类似于多重if选择结构
	case
  when 条件1 then 要显示的值1或语句1;【是值就不要加分号否则报错】
  when 条件2 then 要显示的值2或语句2;【是值就不要加分号否则报错】
  ...
  else 要显示的值n或者语句n;【是值就不要加分号否则报错，else可省略】
  end
*/
# 查询员工工资的情况
/* 
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000，显示C级别
否则显示D级别
*/
SELECT salary,
CASE
WHEN salary>20000 THEN 'A级别'
WHEN salary>15000 THEN 'B级别'
WHEN salary>10000 THEN 'C级别'
ELSE 'D级别'
END AS 级别
FROM employees;
```

##### 单行函数的总结

> 总结各类方法为表格即可

#### 分组函数

> 分组函数：用做统计使用，又称为统计函数、聚合函数、组函数，所有分组函数在运算时都将忽略null值
>
> 注意：和分组函数一同查询的字段有限制，避免出现不规则表格，还是不要一起查询，除非是和group by后的字段一起查询

```mysql
# 求总和：适用于数值类型数据
SELECT SUM(salary) FROM employees;
# 求平均值：适用于数值类型数据
SELECT AVG(salary) FROM employees;
# 求最大值：：适用于数值类型、字符型、日期型等任何类型数据
SELECT MAX(salary) FROM employees;
# 求最小值：适用于数值类型、字符型、日期型等任何类型数据
SELECT MIN(salary) FROM employees;
# 计算个数：适用于数值类型、字符型、日期型等任何类型数据
SELECT COUNT(salary) FROM employees;

#分组函数与DISTINCT搭配使用
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;

#COUNT函数详细介绍
SELECT COUNT(salary) FROM employees;
SELECT COUNT(*) FROM employees; -- 使用较多，只要一行数据中的每一列有一个不为null值也将统计进去，这就是通配符的强大，效率也高，所以一般使用count(*)统计行数
```

### 分组查询

> 语法：select 分组函数，列（要求出现在group by的后面）from 表 [where 筛选条件] group by 分组列表 [order by 子句]
>
> 注意：查询列表必须特殊，要求是分组函数和group by后出现的字段
>
> 总结：
>
> + 分组查询中的筛选条件分为两类
>   + 分组前筛选：数据源处于原始表中，筛选条件处于where 子句中
>   + 分组后筛选：数据源处于分组后的结果集，筛选条件处于having子句中
> + 分组函数做条件肯定是放在having子句中
> + 能使用分组前筛选的优先考虑使用分组前筛选
> + group by子句支持单个字段分组，多个字段分组（多个字段之间使用逗号隔开且没有顺序要求，表达式或函数作为分组条件相对使用较少）
> + 也可以添加排序（排序放在整个分组查询最后）

```mysql
# 查询每个工种的最高工资
SELECT MAX(salary) 最高工资,job_id FROM employees GROUP BY job_id;

# 查询每个位置上的部门个数
SELECT COUNT(department_id),location_id FROM departments GROUP BY location_id;

# 查询每个部门的平均工资
SELECT AVG(salary) AS 平均工资,department_id FROM employees GROUP BY department_id;

#查询邮箱中包含a字符的，每个部门的平均工资【添加分组前的筛选条件】
SELECT email,department_id,AVG(salary) 平均工资 FROM employees WHERE email LIKE '%a%'
GROUP BY department_id;

# 查询有奖金的每个领导手下员工的最高工资
SELECT manager_id,MAX(salary) 最高工资 FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;

# 查询员工个数>2的部门【添加分组后的筛选条件】
SELECT department_id,COUNT(*) 员工个数 FROM employees
GROUP BY department_id HAVING COUNT(*) > 2; 

# 查询每个工种有奖金的员工的最高工资>12000的工种编号与最高工资
SELECT job_id,MAX(salary) FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id HAVING MAX(salary)>12000;

# 查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资
SELECT manager_id,MIN(salary) FROM employees
WHERE manager_id > 102
GROUP BY manager_id HAVING MIN(salary) > 5000;

# 按表达式、函数分组
# 按员工姓名的长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些【表达式分组】
SELECT LENGTH(last_name),COUNT(*) FROM employees
GROUP BY LENGTH(last_name) HAVING COUNT(*) > 5;

# 按多个字段分组
# 查询每个部门的每个工种的员工的平均工资
SELECT AVG(salary) ,department_id,job_id FROM employees
GROUP BY department_id,job_id; #将部门编号和工种编号一致的分为一组

# 添加排序
# 查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示
SELECT AVG(salary)
FROM employees
GROUP BY department_id,job_id
ORDER BY AVG(salary) DESC;
```

### 连接查询

> 连接查询又称为：多表查询，当查询的字段来自于多个表时，就会使用到连接查询
>
> 连接查询的分类：
>
> + 按年代区分
>   + sql92标准：仅仅支持内连接
>   + sql99标准【推荐】：支持内连接+外连接【仅左外、右外】+交叉连接
> + 按功能区分
>   + 内连接
>     + 等值连接
>     + 非等值连接
>     + 自连接
>   + 外连接
>     + 左外连接
>     + 右外连接
>     + 全外连接
>   + 交叉连接

#### sql92语法

##### 等值连接

> + 多表等值连接的结果为多表的交集部分
> + n表连接，至少需要n-1个连接条件
> + 多表的顺序没有要求
> + 一般需要为表起别名
> + 连接可以搭配所有子句使用，如筛选、分组、排序

```mysql
# 查询女生名称与其对应的男朋友的名称
SELECT `name`,boyName FROM beauty,boys
WHERE beauty.`boyfriend_id` = boys.`id`;

# 查询员工名称与其对应的部门名称
SELECT CONCAT(last_name,' ',first_name) 员工名称,department_name 部门名称
FROM employees,departments
WHERE employees.department_id = departments.department_id;

# 查询员工名称、工种号、工种名称【当要查询的字段与所要判断的条件有争议时，也需要指定所要查询的字段来自于哪个表】
-- 当使用表名要限定字段时，太过于繁琐，则可以为表名起别名
/*
 连接查询时为表起别名可提高语句的简洁度
 也可区分多个重名的字段
 注意：一旦为表起了别名，则在虚拟表中就只记录了该表的别名，所以所要查询的字段或用于判断条件的字段就不能使用原来的表名去限定
*/
SELECT CONCAT(last_name,' ',first_name) 员工名称,e.job_id employees的工种号,job_title 工种名称
FROM employees AS e,jobs
WHERE e.job_id = jobs.job_id;

# 可以加筛选如：查询有奖金的员工名称与其对应的部门名称
SELECT CONCAT(last_name,' ',first_name) 员工名称,department_name 部门名称
FROM employees,departments
WHERE employees.department_id = departments.department_id AND commission_pct IS NOT NULL;

# 查询城市名中第二个字符为o的部门名和城市名
SELECT city 城市名,department_name 部门名
FROM locations,departments
WHERE locations.`location_id` = departments.`location_id` AND city LIKE '_o%';

# 可以加分组如：查询每个城市的部门个数
SELECT city,COUNT(*) FROM locations,departments
WHERE locations.`location_id` = departments.`location_id`
GROUP BY city;

# 查询有奖金的每个部门的部门名称和部门的领导编号和该部门的最低工资
SELECT department_name,d.manager_id,MIN(salary)
FROM employees e,departments d
WHERE e.`department_id`=d.`department_id` AND commission_pct IS NOT NULL
GROUP BY e.department_id;

# 可以加排序如：查询每个工种的工种名和员工的个数，并且按照员工个数降序
SELECT job_title,COUNT(*)
FROM jobs,employees e
WHERE jobs.`job_id`=e.`job_id`
GROUP BY e.job_id
ORDER BY COUNT(*) DESC;

# 实现三表连接如：查询员工名、部门名、和部门所在的城市
SELECT last_name,department_name,city
FROM employees e,departments d,locations l
WHERE e.`department_id`=d.`department_id` AND d.`location_id` = l.`location_id`;
```

##### 非等值连接

```mysql
# 查询员工的工资和工资级别
SELECT salary,grade_level
FROM employees e,job_grades j
WHERE e.salary BETWEEN lowest_sal AND highest_sal;
```

##### 自连接

> 自连接是将一张表拆分为多张表操作

```mysql
# 查询员工的名称与其上级的名称
SELECT e.employee_id,e.last_name 员工名称,m.employee_id,m.last_name 领导名称
FROM employees e,employees m
WHERE e.`manager_id` = m.`employee_id`;
```

#### sql99语法

```mysql
select 查询列表
from 表1 别名 【连接类型】
join 表2 别名
on 连接条件
【where 筛选条件】
【group by 分组】
【having 筛选条件】
【order by 排序列表】
【连接类型】分类:
内连接：inner
外连接：
	左外：left【outer】
	右外：right【outer】
	全外：full【outer】
交叉连接：cross
```

##### 内连接

+ 等值连接

  ```mysql
  # 查询员工名及其部门名
  SELECT last_name,department_name
  FROM employees e
  INNER JOIN departments d
  ON e.`department_id`=d.`department_id`;
  
  # 查询名字中包含e的员工名和工种名
  SELECT last_name,job_title
  FROM employees e INNER JOIN jobs j
  ON e.job_id = j.`job_id`
  WHERE last_name LIKE '%e%';
  
  # 查询部门个数>3的城市市名和部门个数
  SELECT city,COUNT(*)
  FROM departments d INNER JOIN locations l
  ON d.`location_id`=l.`location_id`
  GROUP BY city HAVING COUNT(*) > 3;
  
  # 查询哪个部门的部门员工个数>3的部门名称和员工个数，并按个数降序排序
  SELECT department_name,COUNT(*) FROM employees e
  INNER JOIN departments d ON e.`department_id`=d.`department_id`
  GROUP BY department_name HAVING COUNT(*) > 3
  ORDER BY COUNT(*) DESC;
  
  # 查询员工名、部门名、工种名，并按照部门名排序【三表连接】
  SELECT last_name,department_name,job_title
  FROM employees e 
  INNER JOIN departments d ON e.`department_id`=d.`department_id`
  INNER JOIN jobs j ON e.`job_id`=j.`job_id`
  ORDER BY department_name DESC;
  ```

  + 特点
    + 可添加排序、分组、筛选
    + inner可以省略
    + 筛选条件放在where后面，连接条件放在on后面，提高了分离性，便于阅读
    + inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集

+ 非等值连接

  ```mysql
  # 查询员工的工资级别
  SELECT salary,grade_level
  FROM employees e INNER JOIN job_grades j
  ON e.salary BETWEEN j.`lowest_sal` AND j.`highest_sal`;
  
  # 查询工资级别个数>20的个数，并且按工资级别降序排序
  SELECT salary,grade_level,COUNT(*)
  FROM employees e INNER JOIN job_grades j
  ON e.salary BETWEEN j.`lowest_sal` AND j.`highest_sal`
  GROUP BY grade_level HAVING COUNT(*) > 20
  ORDER BY grade_level DESC;
  ```

+ 自连接

  ```mysql
  # 查询员工的名字及其上级的名字
  SELECT e.last_name,m.last_name
  FROM employees e INNER JOIN employees m
  ON e.`manager_id` = m.`employee_id`;
  ```

##### 外连接

> 应用场景：用于查询一个表中有，另一个表没有的记录
>
> 外连接的特点：
>
> + 外连接的查询结果为主表中的所有记录；
>   + 若从表中有与主表匹配的，则显示匹配的值
>   + 若从表中没有与主表匹配的，则显示null值
>   + 外连接查询的结果=内连接结果+主表中有而从表中没有的记录
>   + 全外连接查询的结果=内连接结果+主表中有而从表中没有的记录+从表中有但主表中没有的记录
> + 左外连接：left join左边是主表，右边是从表
> + 右外连接：right join右边是主表，左边是从表
> + 左外和右外交换两个表的顺序，可以实现同样的效果

```mysql
# 查询男朋友不在男神表的女神名【左外连接实现】
SELECT beau.`name`,boy.* FROM beauty beau
LEFT OUTER JOIN boys boy
ON beau.`boyfriend_id`=boy.`id`
WHERE boy.id IS NULL; -- 最好使用主键来判断

# 查询男朋友不在男神表的女神名【右外连接实现】
SELECT `name` FROM boys boy
RIGHT OUTER JOIN beauty beau
ON boy.`id`=beau.`boyfriend_id`
WHERE boy.id IS NULL;

# 查询哪个部门没有员工
SELECT department_name,e.* FROM departments d
LEFT OUTER JOIN employees e
ON d.`department_id`=e.`department_id`
WHERE employee_id IS NULL;
```

##### 交叉连接（笛卡尔积）

```mysql
SELECT b.*,bo.* FROM beauty b 
CROSS JOIN boys bo;
```

#### 连接查询的总结

> + sql92语法与sql99语法的对比
>   + sql99语法支持的功能较多
>   + sql99实现了连接条件和筛选条件的分离，可读性较高

### 子查询

> 子查询：出现在其他语句中的select语句，称为子查询或者内查询；外部的查询语句，称为主查询或外查询
>
> 子查询的分类
>
> + 按照子查询出现的位置
>   + 可以放在select后面【仅仅支持标量子查询】
>   + from后面【支持表子查询】
>   + where或having后面【支持标量子查询、列子查询、行子查询(较少使用)】---重点
>   + exists后面【相关子查询；支持表子查询】
> + 按照结果集的行列数不同
>   + 标量子查询（结果集只有一行一列）
>   + 列子查询（结果集只有一列多行）
>   + 行子查询（结果集有一行多列）
>   + 表子查询（结果集一般为多行多列）

#### where后的标量子查询使用

> where和having后面
>
> + 标量子查询（单行子查询）
> + 列子查询（多行子查询）
> + 行子查询（多列多行）
>
> 特点：
>
> + 子查询都放在小括号内
> + 子查询一般放在条件的右侧
> + 标量子查询，一般搭配着单行操作符使用【>	<	>=	<=	=	<>】
>
> + 列子查询一般搭配着多行操作符使用【IN、ANY/SOME、ALL】
> + 子查询的执行要优先于主查询的执行，因为主查询的条件用到了子查询的结果

```mysql
# 查询工资比Abel高的员工信息
SELECT * FROM employees
WHERE salary > (
	SELECT salary 
	FROM employees 
	WHERE last_name = 'Abel'
);

# 查询job_id与141号员工相同，且salary比143号员工多的员工的姓名、job_id和工资
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id FROM employees WHERE employee_id = 141
) AND salary > (
	SELECT salary FROM employees WHERE employee_id = 143
);

# 返回公司工资最少的员工的last_name,job_id和salary
SELECT last_name,job_id,salary
FROM employees
WHERE salary = (
	SELECT MIN(salary) FROM employees
);

# 查询最低工资大于50号部门最低工资的 id和其最低工资
SELECT department_id,MIN(salary)
FROM employees
GROUP BY department_id
HAVING MIN(salary) > (
	SELECT MIN(salary) FROM employees WHERE department_id = 50
);
```

#### where后的列子查询使用

**需要使用到多行操作符**

|  操作符   |         操作符描述         |
| :-------: | :------------------------: |
| IN/NOT IN |    等于列表中的任意一个    |
| ANY/SOME  | 和子查询返回的某一个值比较 |
|    ALL    |  和子查询返回的所有值比较  |

```mysql
# 返回location_id是1400或1700的部门中的所有员工姓名
SELECT DISTINCT department_id FROM departments WHERE location_id IN(1400,1700);
SELECT last_name FROM employees
WHERE department_id IN(
	SELECT DISTINCT department_id FROM departments WHERE location_id IN(1400,1700)
);

# 返回其它部门中比job_id为'IT_PROG'部门任一工资低的员工的员工号、姓名、job_id以及salary
# ①查询job_id为'IT_PROG'部门 -- department_id为60
SELECT DISTINCT department_id FROM employees WHERE job_id = 'IT_PROG';
# ②查询该job_id为'IT_PROG'部门下的员工的工资
SELECT salary FROM employees WHERE department_id = (SELECT DISTINCT department_id FROM employees WHERE job_id = 'IT_PROG');

# ③查询其它部门员工的工资【即查询出每一个部门的员工工资】
SELECT employee_id,last_name,job_id,salary
FROM employees 
WHERE department_id IN(
	SELECT DISTINCT department_id FROM employees WHERE job_id <> 'IT_PROG'
);
# 最终合成
SELECT employee_id,last_name,job_id,salary
FROM employees 
WHERE department_id IN(
	SELECT DISTINCT department_id FROM employees WHERE job_id <> 'IT_PROG'
)
AND salary < ANY(
	SELECT salary FROM employees WHERE department_id = (
		SELECT DISTINCT department_id FROM employees WHERE job_id = 'IT_PROG'
	)
);
# 返回其它工种中比job_id为'IT_PROG'工种任一工资低的员工的员工号、姓名、job_id以及salary
# ①查询出job_id为'IT_PROG'工种的员工工资
SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG';
# ②查询其它工种的员工工资
SELECT employee_id,last_name,job_id,salary FROM employees WHERE job_id <> 'IT_PROG'
AND salary < ALL(SELECT DISTINCT salary FROM employees WHERE job_id = 'IT_PROG');
```

#### where后面的行子查询的使用（了解）

> 结果集可能为一行多列或多行多列

```mysql
# 查询员工编号最小并且工资最高的员工信息【一般方式】
SELECT * FROM employees WHERE employee_id = (SELECT MIN(employee_id) FROM employees) AND salary = (SELECT MAX(salary) FROM employees);
# 行子查询方式
SELECT * FROM employees WHERE (employee_id,salary) = (
	SELECT MIN(employee_id),MAX(salary) FROM employees
);
```

#### select后面子查询的使用

> 仅仅支持标量子查询，即子查询只能为一行一列的值

```mysql
# 查询每个部门的员工个数【但想把某些部门没有员工的显示为0】
SELECT COUNT(*) FROM employees GROUP BY department_id;
# select子查询方式
SELECT d.*,(
	SELECT COUNT(*) FROM employees e WHERE e.department_id = d.`department_id`
) 员工个数
FROM departments d;

# 查询员工号=102的部门名称
SELECT department_name FROM employees e,departments d
WHERE e.`department_id`=d.`department_id` AND e.`employee_id`=102;
# select子查询方式【繁琐了】
SELECT (
	SELECT department_name FROM departments d INNER JOIN employees e ON e.`department_id`=d.department_id WHERE e.`employee_id`=102
) 部门名称;
```

#### from后面的子查询使用

> 要求将子查询结果充当一张表，要求必须起别名，通过别名找到该结果集

```mysql
# 查询每个部门的平均工资和工资等级
# ①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees GROUP BY department_id;

#②连接①的结果集和job_grades表，筛选条件平均工资between lowest_sal and highest_sal
SELECT avg_dep.avg_sal,j.grade_level
FROM (
	SELECT AVG(salary) avg_sal,department_id
	FROM employees GROUP BY department_id
) avg_dep
INNER JOIN job_grades j
ON avg_dep.avg_sal BETWEEN lowest_sal AND highest_sal;
```

#### exists后面子查询的使用（相关子查询）

> exists（完整的语句），最终的结果只有1和0，就是判断所执行的语句是否有返回值，有没有结果

```mysql
# 查询有员工的部门名
SELECT department_name 
FROM departments d
WHERE EXISTS(
	SELECT * FROM	 employees e WHERE e.`department_id`=d.department_id
);
```

#### 子查询案例

```mysql
# 查询和Zlotkey相同部门的员工姓名和工资
# ①查询出Zlotkey所处的部门
SELECT department_id FROM employees WHERE last_name = 'Zlotkey';-- 80
# ②合成
SELECT last_name,salary FROM employees WHERE department_id = (
	SELECT department_id FROM employees WHERE last_name = 'Zlotkey'
);

# 查询工资比公司平均高的员工的员工号、姓名和工资
# ①查询公司的平均工资
SELECT AVG(salary) FROM employees;
# ②合成
SELECT employee_id,last_name,salary
FROM employees
WHERE salary > (
	SELECT AVG(salary) FROM employees
);

# 查询各部门中工资比本部门平均工资高的员工的员工号、姓名和工资
# ①查询出每个部门中的所有员工工资
SELECT salary ,department_id FROM employees WHERE department_id IN(
	SELECT DISTINCT department_id FROM employees
);
# 查询每个部门的平均工资
SELECT AVG(salary),department_id FROM employees GROUP BY department_id;
# 自己的方式
SELECT employee_id,last_name, salary ,e.department_id FROM employees e WHERE department_id IN(
	SELECT DISTINCT department_id FROM employees
) AND salary > (SELECT AVG(salary) FROM employees m GROUP BY department_id HAVING m.department_id = e.department_id);
# 老师的方式
SELECT employee_id,last_name, salary ,e.department_id
FROM employees e
INNER JOIN (
	SELECT AVG(salary) avg_sal,department_id FROM employees GROUP BY department_id
) avg_dep
ON e.`department_id`=avg_dep.department_id
WHERE salary > avg_dep.avg_sal;

# 查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
# ①查询姓名包含字母u的员工所处的部门号
SELECT DISTINCT department_id FROM employees WHERE last_name LIKE '%u%';
# ②合成
SELECT employee_id,last_name,department_id FROM employees WHERE department_id IN(
	SELECT DISTINCT department_id FROM employees WHERE last_name LIKE '%u%'
);

# 查询在部门的location_id为1700的部门工作的员工的员工号
# ①查询location_id为1700的部门
SELECT DISTINCT department_id FROM departments WHERE location_id = 1700;
SELECT employee_id FROM employees WHERE department_id IN(
	SELECT DISTINCT department_id FROM departments WHERE location_id = 1700
);

# 查询管理者是K_ing的员工姓名和工资
SELECT e.last_name,e.salary,e.manager_id FROM employees e INNER JOIN employees m
WHERE e.`manager_id`=m.`employee_id` AND m.last_name='K_ing'; 

# 查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为姓.名
SELECT CONCAT(last_name,' ',first_name) AS "姓.名" FROM employees WHERE salary = (
	SELECT MAX(salary) FROM employees
)
```

### 分页查询

> 当要显示的数据，一页显示不全，需要分页提交sql请求
>
> 分页查询的特点：
>
> + limit语句放在查询语句的最后
> + 公式为limit (page-1)*size,size;

```mysql
select 查询列表
from 表
【join type】 join 表2
on 连接条件
where 筛选条件
group by 分组字段
having 分组后的筛选
order by 排序的字段
limit offset,size【offset即要显示条目的起始索引，size即要显示的条目个数】
# 若offset为0时，可省略
```

```mysql
# 查询前五条员工信息
SELECT * FROM employees LIMIT 0,5;

# 查询11-25条员工信息
SELECT * FROM employees LIMIT 10,15;

# 有奖金的员工信息，并且将工资较高的前10名显示出来
SELECT * FROM employees WHERE commission_pct IS NOT nulll ORDER BY salary DESC LIMIT 0,10;
```

### 查询语句中涉及到的所有关键字以及执行的先后顺序

```mysql
select 查询列表【⑦】
from 表【①】
连接类型 join 表2【②】
on 连接条件【③】
where 筛选条件【④】
group by 分组列表【⑤】
having 分组后的筛选【⑥】
order by 排序列表【⑧】
limit 偏移，条目数【⑨】
```

### 案例

```mysql
# 查询工资最低的员工信息
SELECT * FROM employees WHERE salary = (
	SELECT MIN(salary) FROM employees
);

#查询平均工资最低的部门信息
SELECT * FROM departments WHERE department_id = (
	SELECT department_id FROM employees GROUP BY department_id ORDER BY AVG(salary) ASC LIMIT 1
);

#查询平均工资最低的部门信息和平均工资
SELECT * FROM (
	SELECT AVG(salary) avg_sal,department_id dep_id FROM employees GROUP BY department_id ORDER BY AVG(salary) ASC LIMIT 1
) avg_dep
INNER JOIN departments d
ON d.department_id = avg_dep.dep_id;


# 查询平均工资最高的job信息
SELECT * FROM jobs WHERE job_id = (
	SELECT jobs_id FROM employees GROUP BY job_id ORDER BY AVG(salary) DESC LIMIT 1	
);

# 查询平均工资高于公司平均工资的部门有哪些
# ①查询公司的平均工资
SELECT AVG(salary) FROM employees;
# ②查询每个部门的平均工资并合成
SELECT AVG(salary) ,department_id FROM employees GROUP BY department_id;
SELECT department_id FROM employees GROUP BY department_id HAVING AVG(salary) > (
	SELECT AVG(salary) FROM employees
);


# 查询出公司所有manager的详细信息
SELECT * FROM employees WHERE employee_id IN(SELECT DISTINCT manager_id FROM employees);

# 查询各个部门中最高工资中最低的那个部门的最低工资
# ①查询每个部门中员工的最高工资排名中最低的"最高工资"
SELECT MAX(salary),department_id dep_id FROM employees GROUP BY department_id ORDER BY MAX(salary) ASC LIMIT 1;
SELECT MIN(salary) FROM employees WHERE department_id = (
	SELECT department_id dep_id FROM employees GROUP BY department_id ORDER BY MAX(salary) ASC LIMIT 1
);

# 查询平均工资最高的部门的manager的last_name、department_id、email、salary
# ①查询平均工资最高的部门的
SELECT last_name,department_id,email,salary FROM employees WHERE employee_id = (
	SELECT DISTINCT manager_id FROM employees WHERE department_id = (
		SELECT department_id FROM employees GROUP BY department_id ORDER BY AVG(salary) DESC LIMIT 1
	) AND manager_id IS NOT NULL
);
```

### union联合查询

> 将多条查询语句的结果合并成一个结果
>
> 应用场景：要查询的结果来自于多个表，且多个表之间没有直接的连接关系，但查询的信息一致时需要使用联合查询
>
> 特点：
>
> + 要求多条查询语句的查询列数是一致的
> + 建议多条查询语句所查询的每一列的类型和顺序最好保持一致
> + 使用union关键字默认会去重复项，可使用union all包含重复项

```mysql
查询语句1
union
查询语句2
union
...

# 查询部门编号>90或者邮箱包含a的员工信息【一般方式】
SELECT * FROM employees WHERE department_id > 90 OR email LIKE '%a%';
# union联合查询方式
SELECT * FROM employees WHERE department_id > 90
UNION
SELECT * FROM employees WHERE email LIKE '%a%';
```

## DML数据操纵语言

> DML（Data Manipulation Language）数据操纵语言
>
> + 插入：insert
> + 修改：update
> + 删除：delete

### 插入操作

> + 插入的值的类型要与列的类型一致或兼容
>
> + 不可以为null的必须插入值，可以为null的可以直接写null或插入时列名和值都省略即为默认值
>
> + 插入时列的顺序可以调换，但值要一一对应，列数和值数必须一致
>
> + 可以 省略列名，默认所有列，而且列的顺序和表中列的顺序一致
>
> 两种插入方式的对比
>
> + 语法一支持插入多行，语法二不支持
> + 语法一支持子查询，语法二不支持

```mysql
# 语法一
insert into 表名(列名,...)
values(值1,...);
# 语法二
insert into 表名 set 列名=值,列名=值,...

# 方式一
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'玛莎','女','1990-4-23','12825614',NULL,2);

# 插入多行
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(14,'玛莎1','女','1990-4-23','12825614',NULL,2),
(15,'玛莎2','女','1990-4-23','12825614',NULL,3),
(16,'玛莎3','女','1990-4-23','12825614',NULL,4);

# 结合子查询
INSERT INTO beauty(id,NAME,phone)
SELECT 23,'dada','1651356';
# 方式二
INSERT INTO beauty SET id=19,NAME='刘涛',phone='666';
```

### 修改操作

#### 修改单表的记录

```mysql
# 语法
update 表名 set 列=新值,列=新值,... where 筛选条件;

# 修改beauty表中名称以玛开头的女神的电话为1388899998
UPDATE beauty SET phone='1388899998' WHERE `name` LIKE '玛%';

# 修改boys表的id号为2的名称为张飞，魅力值为10
UPDATE boys SET boyName='张飞',userCP=10 WHERE id = 2;
```

#### 修改多表的记录（级联更新）

```mysql
# sql92语法
update 表1 别名，表2 别名
set 列=值,...
where 连接条件
and 筛选条件;

# sql99语法
update 表1 别名
inner|left|right| join 表2 别名
on 连接条件
set 列=值,...
where 筛选条件;

# 修改张无忌的女朋友的手机号为114
UPDATE beauty b
INNER JOIN boys bo
ON b.`boyfriend_id`=bo.`id`
SET b.`phone`=114
WHERE bo.`boyName`='张无忌';

# 修改没有男朋友的女神的男朋友编号都为2号张飞
UPDATE boys bo
RIGHT JOIN beauty b
ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2
WHERE bo.id IS NULL;
```

### 删除操作

#### 删除单表的记录

```mysql
# 语法一
delete from 表名 where 筛选条件
# 语法二【注意：该语句是删除全部的表数据即清空，不支持添加筛选条件】
truncate table 表名;

# 删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
```

#### 删除多表的记录（级联删除）

```mysql
# sql92语法
delete 别名【表1的别名，表2的别名（要删除的表别名之间使用逗号隔开，看要删除谁的记录）】
from 表1 别名，表2 别名
where 连接条件
and 筛选条件;
# sql99语法
delete 表1的别名，表2的别名
from 表1 别名
inner|left|right join 表2 别名
on 连接条件
where 筛选条件;

# 删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo
ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='张无忌';

# 删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo
ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';
```

#### delete语句和truncate语句的对比

> + delete语句可以加where条件，truncate不支持
> + 【truncate table 表】清空表数据比【delete from 表】效率高一些
> + 若要删除的表中有自增长列，那么使用delete删除后再插入数据，自增长列的值从断点开始，而truncate清空表数据后，自增长列将重新从1开始
>
> + 使用delete删除具有返回值，使用truncate清空没有返回值
> + truncate删除不能回滚，delete删除可以回滚

## DDL数据定义语言

> DDL（Data Definition Language）数据定义语言

### 库和表的管理

#### 库的管理（创建、修改、删除）

```mysql
# 创建数据库语法
create database [IF NOT EXISTS] 库名称; -- 若库不存在则创建

# 创建数据库books
 CREATE DATABASE IF NOT EXISTS books;
# 修改数据库books的字符集为gbk
ALTER DATABASE books CHARACTER SET gbk;
# 删除数据库books
DROP DATABASE IF EXISTS books;-- 如果存在则删除
```

#### 表的管理（创建、修改、删除）

```mysql
# 创建表的语法
CREATE TABLE 表名(
	列名 列的类型[(长度) 约束 COMMENT '注释信息'],
	列名 列的类型[(长度) 约束 COMMENT '注释信息'],
	...
	列名 列的类型[(长度) 约束 COMMENT '注释信息']
);

# 创建表book
CREATE TABLE book(
	id INT,# 编号
	bookName VARCHAR(20),#图书名称
	price DOUBLE,#价格
	authorId INT,#作者编号
	publishDate DATETIME #出版日期 
);

# 创建表author
CREATE TABLE author(
	id INT,
	authorName VARCHAR(20),
	nation VARCHAR(10)
);

# 修改book表的publishDate列的列名为pubDate
ALTER TABLE book CHANGE COLUMN publishDate pubDate DATETIME;

# 修改book表的pubDate列的类型为时间戳类型
ALTER TABLE book MODIFY COLUMN pubDate TIMESTAMP;

# 为author表添加新的列年薪
ALTER TABLE author ADD COLUMN annual DOUBLE;

# 删除表author的年薪列
ALTER TABLE author DROP COLUMN annual;

# 修改author表的表名为book_author
ALTER TABLE author RENAME TO book_author;

# 删除author表
DROP TABLE [IF EXISTS] book_author;
```

#### 表的复制

```mysql
# 仅仅复制表的结构
CREATE TABLE 复制表名称 LIKE 被复制表名称;
# 复制表的结构+数据
CREATE TABLE 复制表名称 SELECT * FROM 被复制表名称;

# 仅仅复制某些字段结构【设置筛选条件谁都不满足即可，0即false或1=2】
CREATE TABLE 复制表名称 SELECT 列1,列2.. FROM 被复制表名称 WHERE 0;
# 只复制部分数据【追加条件即可】
CREATE TABLE 复制表名称 SELECT 列1,列2 FROM 被复制表名称 WHERE 筛选条件;
```

### 数据类型介绍

> + 数值型：
>   + 整型
>   + 小数
>     + 定点数
>     + 浮点数
> + 字符型：
>   + 较短的文本：char、varchar
>   + 较长的文本：text、blob（较长的二进制数据）
> + 日期型：

#### 整型

> 整型的特点：
>
> + 如果创建表时不设置列的类型是无符号还是有符号，默认是有符号，若像设置为无符号整型，则在列的类型之后添加unsigned关键字即可
> + 若插入数据时数值超出了字段整型的范围，会报out of range异常，并且插入的就是临界值
> + 如果不设置长度，会有默认的长度，长度代表了显示的最大宽度，如果不够会用0在左边填充，但必须搭配zerfill使用

|  整数类型   | 字节 |                   范围                    |
| :---------: | :--: | :---------------------------------------: |
|   Tinyint   |  1   |    有符号：-128-127<br />无符号：0-255    |
|  Smallint   |  2   | 有符号：-32768-32767<br />无符号：0-65535 |
|  Mediumint  |  3   |                 无需记住                  |
| Int/integer |  4   |                 无需记住                  |
|   Bigint    |  8   |                 无需记住                  |

```mysql
CREATE TABLE tab_int(
	t1 INT, #默认有符号，可插入负数
	t2 INT UNSIGNED #设置为无符号，不可插入负数，否则变为临界值0
);
INSERT INTO tab_int VALUES(123,-1);
-- 结果 123  0
CREATE TABLE tab_int(
	t1 INT, #默认长度为11
	t2 INT(7) ZEROFILL#设置长度为7，并且搭配zerofill使用
  -- 那么该字段就自动成为无符号整型，并且在插入数据时，数据值不足7位数，将使用0在左边进行填充，超出无影响
);
INSERT INTO tab_int VALUES(123,123);
-- 结果 123  0000123
INSERT INTO tab_int VALUES(123,123456789);
-- 结果 123  123456789
```

#### 小数

##### 浮点型

| 浮点数类型  | 字节 |   范围   |
| :---------: | :--: | :------: |
| float(M,D)  |  4   | 无需记住 |
| double(M,D) |  8   | 无需记住 |

##### 定点型

|          定点数类型          | 字节 |                             范围                             |
| :--------------------------: | ---- | :----------------------------------------------------------: |
| DECIMAL(M,D)【DEC(M,D)缩写】 | M+2  | 最大取值范围与double相同，精度比double高<br />给定decimal的有效取值范围由M和D决定 |

```mysql
# 测试M和D的意思
CREATE TABLE tab_float(
	t1 FLOAT(5,2),
	t2 DOUBLE(5,2),
	t3 DECIMAL(5,2)
);
INSERT INTO tab_float VALUES(123.45,123.45,123.45);
-- 结果123.45  123.45  123.45
INSERT INTO tab_float VALUES(123.456,123.456,123.456);
-- 结果123.46  123.46  123.46
INSERT INTO tab_float VALUES(123.4,123.4,123.4);
-- 结果123.40  123.40  123.40
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);
-- 结果999.99  999.99  999.99
-- 由此得出D是小数位数，超出则四舍五入，不足则补0
-- 由此得出M是包含小数位在内的总位数，超出则变为临界值
-- M,D都可以省略，若是DECIMAL的话默认M为10，D为0；若是float和double，则会根据插入的数值的精度来随机变化
-- 定点型的精度较高，如果要求插入数值的较高如货币运算则可考虑使用
-- 原则：所选择的类型越简单越好，能保存数值的类型越小越好
```

#### 字符型

> 可用于保存MySQL中较短的字符串即较短的文本的两种类型：char、varchar
>
> + char代表固定长度的字符，比较耗费空间（无论存入的数据有多少字符都开辟M个字符的空间），但效率高
> + varchar代表可变长度的字符，比较节省空间（根据存入的数据开辟空间），但是效率低
>
> 较长的文本：text、blob（较大的二进制）

| 字符串类型 | 最多字符数 |                        描述及存储需求                        |
| :--------: | :--------: | :----------------------------------------------------------: |
|  char(M)   |     M      | M为0-255之间的整数<br />M可以省略，默认为1，即可以存储性别男或女 |
| varchar(M) |     M      |            M为0-65535之间的整数<br />M不可以省略             |

**其它补充类型**

+ binary和varbinary用于保存较短的二进制

+ enum用于保存枚举

  > Enum类型又称为枚举类型，要求插入的值必须属于列表中指定的值之一
  >
  > + 若列表成员为1-255，则需要一个字节存储
  > + 若列表成员为255-65535则需要两个字节存储
  > + 最多需要65535个成员！

  ```mysql
  CREATE TABLE tab_enum(
  	t1 ENUM('a','b','c')
  );
  # 插入时不区分大小写
  INSERT INTO tab_enum VALUES('a');
  -- 结果 a
  INSERT INTO tab_enum VALUES('B');
  -- 结果 b
  INSERT INTO tab_enum VALUES('e');
  -- 插入失败
  ```

+ set用于保存集合

  > Set类型和Enum类型类似，里面可以保存0-64个成员，和Enum类型最大的区别就是SET类型一次可以选取多个成员，而Enum只能选择一个；根据成员个数不同，存储所占的字节也不同
  >
  > + 1-8个成员占1字节
  > + 9-16个成员占2字节
  > + 17-24个成员占3字节
  > + 25-32个成员占4字节
  > + 33-64个成员占8字节

  ```mysql
  CREATE TABLE tab_set(
  	t1 SET('a','b','c')
  );
  # 插入时不区分大小写
  INSERT INTO tab_set VALUES('a');
  -- 结果 a
  INSERT INTO tab_enum VALUES('a,c');
  -- 结果 a,c
  INSERT INTO tab_enum VALUES('A,b,C');
  -- 结果 a,b,c
  ```

#### 日期型

> datetime和timestamp的区别
>
> + Timestamp支持的时间范围较datetime小
> + timestamp和实际时区有关，更能反映实际的日期，而datetime则只能反映出插入时的当地时区，即不受时区影响
> + timestamp的属性受MySQL版本和SQLMode的影响很大

| 日期和时间类型 | 字节 |       最小值        |       最大值        |     描述      |
| :------------: | :--: | :-----------------: | :-----------------: | :-----------: |
|      date      |  4   |     1000-01-01      |     9999-12-31      |   保存日期    |
|    datetime    |  8   | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 | 保存日期+时间 |
|   timestamp    |  4   |   19700101080001    |  2038年的某个时刻   | 保存日期+时间 |
|      time      |  3   |     -838:59:59      |      838:59:59      |   保存时间    |
|      year      |  1   |        1901         |        2155         |    保存年     |

```mysql
CREATE TABLE tab_time(
	t1 DATETIME,
	t2 TIMESTAMP
);
INSERT INTO tab_time VALUES(NOW(),NOW());
-- 结果 2021-08-26 22:23:56  2021-08-26 22:23:56
# 更改时区
SET time_zone='+9:00';
SELECT * FROM tab_time;
-- 结果 2021-08-26 22:23:56  2021-08-26 23:23:56 -- 晚一小时
```

### 常见约束

> 约束：即一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性
>
> 约束的分类：
>
> + NOT NULL：非空约束，用于保证该字段的值不能为空【如姓名、学号等】
> + DEFAULT：默认约束，用于保证该字段有默认值【如性别】
> + PRIMARY KEY：主键约束，用于保证该字段的值具有唯一性，并且不能为空【如学号、员工编号】
> + UNIQUE：唯一约束，用于保证该字段的值具有唯一性，并且可以为空【如座位号】
> + CHECK：检查约束【如年龄、性别，但MySQL中不支持该约束，但兼容不报错】
> + FOREIGN KEY：外键约束，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值【如学生表的专业编号、员工表的部门编号、员工表的工种编号】
>
> 添加约束的时机
>
> + 创建表时
> + 修改表时
>
> 约束的添加分类
>
> + 列级约束：六大约束语法上都支持，但是外键约束没有效果，一个字段支持添加多个约束，多个约束之间使用空格隔开
> + 表级约束：除了非空和默认约束，其他约束都支持

#### 创建表时添加约束

```mysql
# 创建学生数据库
CREATE DATABASE students;

#创建专业表
CREATE TABLE major(
	id INT PRIMARY KEY,
	majorName VARCHAR(20)
);

# 创建学生信息表时添加约束之添加列级约束
-- 直接在字段名和类型后面追加约束类型即可，只支持默认、非空、主键、唯一约束（检查约束和外键约束都不支持，以下只是演示写入后语法也不出错）
CREATE TABLE stu_info(
	id INT PRIMARY KEY,#主键约束
	stuName VARCHAR(20) NOT NULL,#非空约束
	gender CHAR(1) CHECK(gender IN('男','女')), #检查约束
	seatNum INT UNIQUE,# 唯一约束
	age INT DEFAULT 18, #默认约束
	majorId INT REFERENCES major(id) #外键，引用major表的id
);

# 查看stu_info表中所有的索引，包括主键、外键、唯一
SHOW INDEX FROM stu_info;
```

```mysql
# 创建学生信息表时添加约束之添加表级约束
-- 在各个字段的最下面添加约束[]中的可以省略，省略后默认的自定义约束名就是字段名
-- [constraint 自定义约束名] 约束类型(字段名)
 CREATE TABLE stu_info(
	id INT,
	stuName VARCHAR(20),
	gender CHAR(1),
	seatNum INT,
	age INT,
	majorId INT,
	CONSTRAINT pk PRIMARY KEY(id),#主键
	CONSTRAINT uq UNIQUE(seatNum),#唯一键
	CONSTRAINT ck CHECK(gender IN('男','女')),#检查约束
	CONSTRAINT fk_stu_info_major FOREIGN KEY(majorId) REFERENCES major(id)#外键
);
# 可以发现外键添加上了
SHOW INDEX FROM stu_info;
```

```mysql
# 通用的写法：
CREATE TABLE IF EXISTS stu_info(
	id INT PRIMARY KEY,#主键约束
	stuName VARCHAR(20) NOT NULL,#非空约束
	gender CHAR(1), #检查约束无效
	seatNum INT UNIQUE,# 唯一约束
	age INT DEFAULT 18, #默认约束
  majorId INT,
  CONSTRAINT fk_stu_info_major FOREIGN KEY(majorId) REFERENCES major(id)#表级外键约束
);
```

#### 主键约束和唯一约束的区别

> + 二者都可以保证唯一性
> + 主键不允许为空，唯一约束允许为空，但只允许有一个为空值，不能存在两个空值，否则就不唯一就重复了
> + 主键一个表中最多只有一个，唯一约束可以有多个
> + 主键和唯一键都允许多个字段组合成一个主键或者一个唯一键，但不推荐

#### 外键的特点

> + 要求在从表设置外键关系
> + 从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
> + 主表中的关联列必须是一个key（一般是主键或唯一键）
> + 要求插入数据时，应该先插入主表中的数据，再插入从表中的数据；相反，删除数据时应先删除从表中的数据，再删除主表中的数据

#### 修改表时添加、删除约束

```mysql
# 语法
-- ①添加列级约束
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 新约束;
-- ②添加表级约束
ALTER TABLE 表名 ADD [CONSTRAINT 自定义约束名] 约束类型(字段名) 【外键的引用】;

# 添加非空约束
ALTER TABLE stu_info MODIFY COLUMN stuName VARCHAR(20) NOT NULL;

# 添加默认约束
ALTER TABLE stu_info MODIFY COLUMN age INT DEFAULT 18;

# 添加主键约束
-- ①列级约束写法
ALTER TABLE stu_info MODIFY COLUMN id INT PRIMARY KEY;
-- ②表级约束写法
ALTER TABLE stu_info ADD PRIMARY KEY(id);

# 添加唯一约束
-- ①列级约束写法
ALTER TABLE stu_info MODIFY COLUMN seatNum INT UNIQUE;
-- ②表级约束写法
ALTER TABLE stu_info ADD UNIQUE(seatNum);

# 添加外键约束
ALTER TABLE stu_info ADD CONSTRAINT fk_stu_info_major FOREIGN KEY(majorId) REFERENCES major(id);

# 删除非空约束
ALTER TABLE stu_info MODIFY COLUMN stuName VARCHAR(20) NULL;
# 删除默认约束
ALTER TABLE stu_info MODIFY COLUMN age INT;
# 删除主键约束
-- 字段名可以不用写，因为一个表就一个主键列
ALTER TABLE stu_info DROP PRIMARY KEY;
# 删除唯一约束
ALTER TABLE stu_info DROP INDEX seatNum;
# 删除外键约束
ALTER TABLE stu_info DROP FOREIGN KEY fk_stu_info_major;
```

#### 列级约束和表级约束的对比

> + 列级约束声明在列的后面，约束的语法都支持，但外键约束没有效果，且不可以自定义约束名称
> + 表级约束声明在所有列的下面，默认和非空约束不支持，其它都支持，且可以自定义约束的名称（主键的自定义名称无效，全都会被替换为PRIMARY）

### 标识列

> 又称为自增长列，可以不用手动插入值，系统提供默认的序列值

#### 创建表时设置标识列

> 标识列的特点
>
> + 标识列必须与key搭配使用，如主键或唯一键
> + 一个表中最多只能有一个标识列
> + 标识列的类型只支持数值型，一般为int型，小数也可以
> + 标识列可以通过SET auto_increment_increment = 3;来设置步长，可以通过手动插入值设置起始值

```mysql
# 创建表时设置标识列，在约束后添加AUTO_INCREMENT关键字
CREATE TABLE tab_identity(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20)
);

# 更改自增长值（步长）【默认自增长值为1】
SET auto_increment_increment = 3;

# 查看当前auto_increment变量值
-- auto_increment_increment代表自增长值【支持修改】
-- auto_increment_offset代表起始值【不支持修改，想要设置起始值可以手动插入数据时指定一个数据起始值】
SHOW VARIABLES LIKE '%auto_increment%';
```

#### 修改表时设置标识列

```mysql

```

## TCL事务控制语言

> TCL（Transaction Control Language）事务控制语言
>
> 事务：一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行；在这个单元中，每个MySQL语句是相互依赖的。而整个单独单元作为一个不可分割的整体，如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会回滚。所有受到影响的数据将返回到事物开始以前的状态；如果单元中的所有SQL语句均执行成功，则事物被顺利执行

### 事务的ACID特性

> + 原子性（Atomicity）：原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生
> + 一致性（Consistency）：事务必须使数据库从一个一致性状态变换到另外一个一致性状态
> + 隔离性（Isolation）：事务的隔离性是指一个事务的执行不能被其它事务干扰，即一个事务内部的操作及使用的数据对并发的其它事务是隔离的，并发执行的各个事务之间不能互相干扰
> + 持久性（Durability）：持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响

### 事务的创建

> 隐式事务：事务没有明显的开启和结束的标记
>
> + 比如insert、update、delete语句
>
> 显式事务：事务具有明显的开启和结束的标记
>
> + 前提：必须设置自动提交功能为禁用（set autocommit=0）

```mysql
# 开启事务
SET autocommit = 0;
START TRANSACTION;-- 该语句是可选的，这条语句不写也会默认开启事务
-- 编写一组事务的sql语句（select、insert、update、delete）
-- ...
# 步骤3：结束事务
COMMIT;-- 提交事务
ROLLBACK;-- 或回滚事务【一般用于出现异常时回滚】
```

### 数据库的隔离级别

> 对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题：
>
> + 脏读：对于两个事务T1,T2，T1读取了已经被T2更新但还没有提交的字段，之后，若T2回滚，T1读取的内容就是临时且无效的
> + 不可重复读：对于两个事务T1,T2，T1读取了一个字段，随后T2更新了该字段，之后，T1再次读取同一个字段，值就发生了变化
> + 幻读：对于两个事务T1,T2，T1从一个表中读取了一个字段，随后T2在该表中插入了一些新的行，之后T1再次读取同一个表，就会多出几行数据
>
> 数据库事务的隔离性：数据库系统必须具有隔离并发运行各个事务的能力，使他们不会相互影响，避免各种并发问题
>
> 一个事务与其它事务隔离的程度称为隔离级别，数据库规定了多种事务隔离级别，不同隔离级别对应不同的干扰程度，隔离级别越高，数据库的一致性就越好，但并发性就越弱

**数据库提供了4种事务的隔离级别**

|             隔离级别             |                             描述                             |
| :------------------------------: | :----------------------------------------------------------: |
| READ UNCOMMITTED（读未提交数据） | 允许事务读取未被其它事务提交的变更<br />==脏读、不可重复读和幻读的问题都会出现== |
|  READ COMMITTED（读已提交数据）  | 只允许事务读取已经被其它事务提交的变更，可避免脏读<br />==不可重复读、幻读问题仍然存在== |
|   REPEATABLE READ（可重复读）    | 确保事务可以多次从一个字段中读取相同的值，<br />在这个事务持续期间，禁止其它事务对这个字段进行更新<br />可避免脏读和不可重复读<br />==幻读的问题仍存在== |
|      SERIALIZABLE（串行化）      | 确保事务可以从一个表中读取相同的行，在这个事务持续期间<br />禁止其它事务对该表执行插入、更新和删除操作<br />==所有并发问题都可以避免，但性能十分低下== |

+ Oracle支持的两种事务隔离级别：READ COMMITTED、SERIALIZABLE。Oracle默认的事务隔离级别为READ COMMITTED
+ MySQL支持4种事务隔离级别，MySQL默认的事务隔离级别为REPEATABLE READ

```mysql
# 查询当前的隔离级别
SELECT @@tx_isolation;
# 设置当前连接/全局隔离级别为最低的隔离级别READ UNCOMMITTED
SET SESSION/GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```

### 回滚点的使用

```mysql
SET autocommit=0;
START TRANSACTION;
DELETE FROM account WHERE id = 25;
SAVEPOINT a;#设置保存点，保存点只能搭配回滚使用
DELETE FROM account WHERE id = 28;
ROLLBACK TO a;# 回滚到保存点，回滚完后，25号被执行了也就是删除了25号，28号没被执行
```

### delete和truncate在事务使用时的区别

> delete支持回滚，truncate不支持回滚，truncate还是会清空全部数据

# 第四章 视图

> 视图：即虚拟表，和普通表一样使用，使mysql5.1版本出现的新特性，是通过表动态生成的数据；视图中行和列的数据都来自于自定义视图的查询中使用的表，并且都在使用视图时动态生成的，只保存了sql逻辑，不保存查询结果
>
> 视图的应用场景：
>
> + 多个地方用到同样的查询结果
> + 该查询结果使用的sql语句较复杂

## 视图的创建和使用

```mysql
# 查询邮箱中包含a字符的员工名、部门名和工种信息
# ①将三表连接的结果封装到视图中
CREATE VIEW my_view1 AS
SELECT last_name,department_name,job_title
FROM employees e
INNER JOIN departments d ON e.department_id=d.department_id
INNER JOIN jobs j ON e.job_id=j.job_id;
# ②从视图中直接查询数据
SELECT *
FROM my_view1
WHERE last_name LIKE '%a%';

# 查询各部门的平均工资级别
# ①查询出每个部门的平均工资并封装为一个视图
CREATE VIEW my_view2 AS
SELECT AVG(salary) avg_sal,department_id
FROM employees e
GROUP BY department_id;
# ②连接视图和表，查出各部门的平均工资级别
SELECT avg_sal,grade_level
FROM my_view2 m
INNER JOIN job_grades j
ON m.`avg_sal` BETWEEN j.`lowest_sal` AND j.`highest_sal`;

# 查询平均工资最低的部门信息
SELECT MIN(avg_sal),d.* FROM my_view2 m
INNER JOIN departments d ON m.`department_id`=d.`department_id`;
```

## 视图的修改

```mysql
# 语法一：视图存在则修改，视图不存在则创建
CREATE OR REPLACE VIEW 视图名 AS
查询语句

# 语法二：直接修改视图
ALTER VIEW 视图名 AS
查询语句
```

## 视图的查看和删除

```mysql
# 删除视图语法：可以同时删除多个视图，要求用户权限要足够
DROP VIEW 视图名,视图名,..;

# 查看视图语法一：
DESC 视图名;
# 查看视图语法二：
SHOW CREATE VIEW 视图名;
```

## 视图的更新

> 视图的可更新性与创建视图时定义的查询语句有关系，以下类型的视图是不能进行更新操作的：
>
> + 视图包含如下关键字的sql语句
>   + 分组函数
>   + distinct
>   + group by
>   + having
>   + union或union all
> + select中包含子查询语句
> + join
> + from一个不能更新的视图
> + where子句的子查询引用了from子句中的表
> + 常量视图

```mysql
CREATE VIEW my_emp AS
SELECT last_name,email
FROM employees;

SELECT * FROM my_emp;
SELECT * FROM employees;

# 对视图进行更新操作
# 向视图中插入数据【所插入的数据不仅对视图产生了影响，也对原始表产生了影响，即二者都被插入了数据】
INSERT INTO my_emp VALUES('Martha','Martha@qq.com');
# 修改视图中last_name 为 Martha的名称为Beth【数据的修改也会作用在视图表与原始表上】
UPDATE my_emp SET last_name='Beth' WHERE last_name = 'Martha';
# 删除视图中last_name为Beth的数据【数据的删除也会作用在视图表与原始表上】
DELETE FROM my_emp WHERE last_name = 'Beth';
-- 对视图的更新操作会降低数据库的逻辑独立性，破坏了原始数据，降低了安全性，所以一般不建议更新视图
```

## 视图的好处

> + 重用sql语句
> + 简化复杂的sql操作，不必知道其查询细节
> + 保护原始数据，提高了安全性

## 视图与表的对比

> + 视图的创建方式为CREATE VIEW；表的创建方式为CREATE TABLE
> + 视图基本没有占用物理空间只是保存了sql逻辑；表占据了一定数据所需要的物理空间
> + 视图支持增删改查，一般不能增删改，一般只用作查询；表是支持增删改查的

# 第五章 变量

> 变量分为如下两种：
>
> + 系统变量：根据系统变量的作用域不同分为以下两种
>   + 全局变量
>   + 会话变量
> + 自定义变量
>   + 用户变量
>   + 局部变量

## 系统变量

> 系统变量的说明：变量由系统提供，不是用户定义，属于服务器层面，服务器每次启动将为所有的全局变量赋上初始值，针对于所有的会话（连接）有效，但是不能跨服务器，跨服务器就无效了，若要永久修改全局变量的值，那么只能修改配置文件，一般也不会这么去做
>
> 注意：如果是全局系统变量，则需要指明GLOBAL关键字，如果是会话级别，则需要加SESSION，如果不写，则默认SESSION

```mysql
# 使用的语法
# 查看全局或会话的系统变量【[]意为默认为session】
SHOW GLOBAL|[SESSION] VARIABLES;
# 查看全局或会话中满足条件的部分变量
SHOW GLOBAL|[SESSION] VARIABLES LIKE '%char%';
# 查看指定的某个系统变量的值
SELECT @@global|[SESSION].系统变量名;
# 为某个系统变量赋值
-- 方式一
SET GLOBAL|[SESSION] 系统变量名=值;
-- 方式二
SET @@global|[SESSION].系统变量名=值;
```

## 会话变量

> 会话变量：所有的会话变量更改操作仅仅针对于当前会话（连接）有效

```mysql
# 查看所有的会话变量
SHOW VARIABLES;
SHOW SESSION VARIABLES;

# 查看部分的会话变量
SHOW VARIABLES LIKE '%char%';
SHOW SESSION VARIABLES LIKE '%char%';

# 查看指定的某个会话变量
SELECT @@tx_isolation;
SELECT @@session.tx_isolation;

# 为某个会话变量赋值
-- 方式一
SET @@tx_isolation='read-uncommitted';
SET @@session.tx_isolation='read-uncommitted';
-- 方式二
SET SESSION tx_isolation='read-committed';
```

## 自定义变量之用户变量

> 说明：
>
> + 变量是由用户自定义的，不是由系统提供的，自定义变量的使用比较灵活
> + 用户变量的作用域只针对于当前会话（连接）有效，同于会话变量的作用域
> + 用户变量可以应用在任何地方，也就是begin end的里面或外面
>
> 使用步骤：声明 赋值 使用（查看、比较、运算）

```mysql
# 声明用户变量并初始化的三种方式，赋值的操作符为=或:=
SET @用户变量名=值;
SET @用户变量名:=值;
SELECT @用户变量名:=值;

# 赋值（更新用户变量的值）
# 方式一：通过SET或SELECT
SET @用户变量名=值;
SET @用户变量名:=值;
SELECT @用户变量名:=值;
# 方式二：通过SELECT INTO
SELECT 字段 INTO @变量名 FROM 表;

# 查看用户变量的值
SELECT @用户变量名;
```

```mysql
# 案例演示
# 声明用户变量并初始化
SET @name='Martha';
SET @name=100;
SET @count=1;

# 赋值第二种方式【将员工的总数用emp_count用户变量保存】
SELECT COUNT(*) INTO @emp_count FROM employees;

# 查看用户变量@emp_count
SELECT @emp_count;
```

## 自定义变量之局部变量

> 说明：
>
> + 作用域仅仅在定义它的begin end中有效，且只能应用在begin end中的第一句话

```mysql
 # 局部变量的声明
 DECLARE 变量名 类型;
 DECLARE 变量名 类型 DEFAULT 值;
 
 # 赋值
 # 方式一：通过SET或SELECT
SET 局部变量名=值;
SET 局部变量名:=值;
SELECT @用户变量名:=值;
# 方式二：通过SELECT INTO
SELECT 字段 INTO 局部变量名 FROM 表;
```

## 用户变量和局部变量的对比

> + 用户变量的作用域限于当前会话；局部变量限于begin end中
> + 用户变量可应用在会话中的任何位置；局部变量仅限于begin end中的第一句话
> + 用户变量在声明时必须加上@符号；局部变量一般不用加@符号，除非是使用select声明时需要加上@符号
> + 用户变量在声明时不需要指明变量的类型；局部变量在声明时需要指明变量类型

# 第六章 存储过程和函数

> 存储过程和函数类似于Java中的方法

## 存储过程

> 存储过程可以理解为一组预先编译好的SQL语句的集合，可以理解成批处理语句，但是比批处理语句更加强大
>
> + 提高了代码的重用性
> + 简化了操作
> + 减少了编译次数并且减少了数据库服务器的连接次数，提高了效率

```mysql
# 存储过程的创建语法
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
 存储过程体（一组合法有效的SQL语句）
END
-- 注意：参数列表包含三部分（参数模式 参数名 参数类型）
-- ①参数模式分为三种模式：
-- IN：该参数可以作为输入，也就是该参数需要调用方传入值
-- OUT：该参数可以作为输出，也就是该参数可以作为返回值
-- INOUT：该参数既可以作为输入也可以作为输出，也就是该参数既需要传入值，又可以返回值
-- ②如果存储过程体仅仅只有一句话，BEGIN END 可以省略；
-- 存储过程体的每条SQL语句的结尾要求必须加上分号
-- 存储过程的结尾可以使用DELIMITER重新设置
-- 语法 DELIMITER 结束标记

# 调用存储过程体
CALL 存储过程名(实参列表);
```

### 空参的存储过程

```mysql
DELIMITER $ -- 设置结束标记，除了存储过程体使用分号，其后的所有语句都使用该标记结尾
CREATE PROCEDURE my_pro()
BEGIN
 INSERT INTO admin(username,`password`) VALUES('a','123'),
 ('a','123'),
 ('a','123'),
 ('a','123'),
 ('a','123');
END $
# 调用存储过程，在sqlyog中可能会有些bug，建议存储过程的代码在命令行中执行
CALL my_pro()$
```

### 创建带in模式参数的存储过程

```mysql
# 创建存储过程实现：根据女神名，查询对应的男神信息
DELIMITER $
CREATE PROCEDURE my_pro2(IN beautyName VARCHAR(20))
BEGIN
	SELECT * FROM boys bo
	INNER JOIN beauty b ON bo.id=b.`boyfriend_id`
	WHERE b.name=beautyName;
END $
DELIMITER ;
CALL my_pro2('柳岩');

# 创建存储过程实现：用户是否登录成功
DELIMITER $
CREATE PROCEDURE my_pro3(IN username VARCHAR(20),IN PASSWORD VARCHAR(20))
BEGIN
	DECLARE isSuccess INT DEFAULT 0;#声明并初始化
	SELECT COUNT(*) INTO isSuccess #给局部变量赋值
	FROM admin a
	WHERE a.username = username AND a.password = PASSWORD;
	SELECT IF(isSuccess,'成功','失败');# 打印成功或者失败
END $
DELIMITER ;
CALL my_pro3('a','1233');
```

### 创建带out模式参数的存储过程

```mysql
# 根据女神名，返回对应的男神名
DELIMITER $
CREATE PROCEDURE my_pro4(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20))
BEGIN
	SELECT bo.boyName INTO boyName # 将查询出的变量直接赋值给形参boyName并返回
	FROM boys bo
	INNER JOIN beauty b
	ON bo.id=b.boyfriend_id
	WHERE b.name=beautyName;
END $
DELIMITER ;
# 定义一个用户变量用于接收存储过程返回值
SET @boyName = '';
# 调用存储过程并置入用户变量接收返回值
CALL my_pro4('王语嫣',@boyName);
# 输出变量值
SELECT @boyName;

# 根据女神名，返回对应的男神名及其魅力值
DELIMITER $
CREATE PROCEDURE my_pro5(IN beautyName VARCHAR(20),OUT boyName VARCHAR(20),OUT userCP INT)
BEGIN
	SELECT bo.boyName,bo.userCP INTO boyName,userCP # 将查询出的变量依次赋值给形参
	FROM boys bo
	INNER JOIN beauty b
	ON bo.id=b.boyfriend_id
	WHERE b.name=beautyName;
END $
DELIMITER ;
# 创建两个用户变量
SET @boy_name = '';
SET @userCP=0;
# 调用存储过程并置入用户变量用于接收返回值
CALL my_pro5('王语嫣',@boy_name,@userCP);
# 输出变量值
SELECT @boy_name,@userCP;
```

### 创建带inout模式参数的存储过程

```mysql
# 传入a和b两个值，最终a和b都翻倍并返回
DELIMITER $
CREATE PROCEDURE my_pro6(INOUT a INT,INOUT b INT)
BEGIN
	# 为局部变量赋值完后，由于inout模式，该参数值会自动返回
	SET a=a*2;
	SET b=b*2;
END $
DELIMITER ;
# 创建两个用户变量并赋值
SET @a=2;
SET @b=4;
# 调用存储过程进行传参
CALL my_pro6(@a,@b);
# 查询两个用户变量的值
SELECT @a,@b;
```

### 存储过程的案例

```mysql
# 创建存储过程或函数实现传入用户名和密码，使其插入到admin表中
DELIMITER $
CREATE PROCEDURE my_pro1(IN username VARCHAR(20),IN `password` VARCHAR(20))
BEGIN
	INSERT INTO admin(username,`password`) VALUES(username,`password`);
END $
DELIMITER ;
CALL my_pro1('aa','123456');
# 创建存储过程或函数实现传入女神编号，返回女神名称和女神电话
DELIMITER $
CREATE PROCEDURE my_pro2(IN beauty_id INT,OUT beautyName VARCHAR(20),OUT beauty_phone VARCHAR(20))
BEGIN
	SELECT `name`,phone INTO beautyName,beauty_phone
	FROM beauty
	WHERE id=beauty_id;
END $
DELIMITER ;
SET @beautyName='';
SET @beautyPhone='';
CALL my_pro2(1,@beautyName,@beautyPhone);
SELECT @beautyName,@beautyPhone;
# 创建存储过程或函数实现传入两个女神的生日，并返回大小
DELIMITER $
CREATE PROCEDURE my_pro3(IN borndate1 DATETIME,IN borndate2 DATETIME,OUT result INT)
BEGIN
	SELECT DATEDIFF(borndate1,borndate2) INTO result;
END $
DELIMITER ;
SET @result='';
CALL my_pro3('1988-02-03 00:00:00','1987-12-30 00:00:00',@result);
SELECT @result;
```

### 存储过程的删除

```mysql
# 删除存储过程的语法【一次只能删除一个存储过程】
DROP PROCEDURE 存储过程名称;
```

### 存储过程信息的查看

```mysql
# 查看存储过程信息的语法
SHOW CREATE PROCEDURE 存储过程名称;
```

## 函数

> 存储过程：可以有0个返回值，也可以有多个返回值，适合做批量插入、批量更新等增删改操作
>
> 函数：必须且只能有一个返回值，适合做处理数据后返回一个结果

### 函数的创建和调用

```mysql
# 创建函数的语法
CREATE FUNCTION 函数名(参数列表) RETURNS 返回类型
BEGIN
	函数体
END
-- 注意：参数列表包含两个部分
	参数名  参数类型
-- 函数体：肯定会有return语句，如果没有会报错【如果return语句没有放在函数体的最后也不报错，但不建议这么做】
-- 和存储过程相似，如果函数体仅仅只有一句话，BEGIN END 可以省略；
-- 使用delimiter语句设置结束标记

#  调用函数的语法
SELECT 函数名(参数列表);
```

### 案例演示函数的使用

```mysql
# 无参有返回值的函数——返回公司的员工个数
DELIMITER $
CREATE FUNCTION my_func() RETURNS INT
BEGIN
	DECLARE result INT;#定义局部变量
	SELECT COUNT(*) INTO result FROM employees;
	RETURN result;
END $
SELECT my_func();

# 有参有返回值的函数——根据员工名称返回其工资
DELIMITER $
CREATE FUNCTION my_func2(empName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	SET @sal=0; #也可定义用户变量，用户变量作用域涉及整个会话
	SELECT salary INTO @sal FROM employees
	WHERE last_name=empName;
	RETURN @sal;
END $
DELIMITER ;
SELECT my_func2('De Haan');

# 根据部门名，返回该部门的平均工资
DELIMITER $
CREATE FUNCTION my_func3(depName VARCHAR(20)) RETURNS DOUBLE
BEGIN
	SET @avg_sal=0;
	SELECT AVG(salary) INTO @avg_sal FROM employees e
	INNER JOIN departments d
	ON e.department_id=d.department_id
	WHERE d.department_name=depName;
	RETURN @avg_sal;
END $
DELIMITER ;
SELECT my_func3('Adm');
```

### 函数的查看和删除

```mysql
# 查看函数定义的语法
SHOW CREATE FUNCTION 函数名称;

# 删除函数的语法
DROP FUNCTION 函数名称;
```

# 第七章 流程控制结构

> 顺序结构：程序从上往下依次执行
>
> 分支结构：程序从两条或多条路径中选择一条去执行
>
> 循环结构：程序在满足一定条件的基础上，重复执行一段代码

## 分支结构之if函数

> if函数能够实现简单的双分支
>
> if函数的执行顺序为，若表达式1成立则返回表达式2的值，否则返回表达式3的值
>
> if函数可以应用在任何地方

```mysql
# 语法
SELECT IF(表达式1，表达式2，表达式3);
```

## 分支结构之case结构

> 类似于Java中的switch语句，一般用于实现等值判断
>
> 类似于Java中的多重if语句，一般用于实现区间判断
>
> case结构的特点：
>
> + case结构既可以作为表达式，嵌套在其它语句中使用【应用在任何地方，如BEGIN END中或外部】，又可以作为独立的语句去使用，但作为独立的语句时，只能放在BEGIN END中使用
>
> + 如果WHEN中的值满足或条件成立，则执行对应的THEN后面的语句或值，并且结束CASE；若都不满足，则执行ELSE中的语句或值
> + ELSE可以省略，如果ELSE省略了，并且所有的WHEN条件都不满足，则将返回NULL

```mysql
# 语法一
CASE 变量|表达式|字段
WHEN 要判断的值 THEN 返回的值1[或语句1;]
WHEN 要判断的值 THEN 返回的值2[或语句2;]
...
ELSE 要返回的值n[或语句n;]
END [CASE];

# 语法二
CASE 变量|表达式|字段
WHEN 要判断的条件1 THEN 返回的值1[或语句1;]
WHEN 要判断的条件2 THEN 返回的值2[或语句2;]
...
ELSE 要返回的值n[或语句n;]
END [CASE];

# case结构作为独立语句使用的，即仅仅只能在BEGIN END中使用的演示
DELIMITER $
CREATE PROCEDURE my_pro(IN score INT)
BEGIN
	CASE score
	WHEN score BETWEEN 90 AND 100 THEN SELECT 'A';
	WHEN score BETWEEN 80 AND 90 THEN SELECT 'B';
	WHEN score BETWEEN 60 AND 80 THEN SELECT 'C';
	ELSE SELECT 'D';
	END CASE;
END $
DELIMITER ;
CALL my_pro(30);
```

## 分支结构之if结构

> if结构只能应用在BEGIN END中

```mysql
# 语法
IF 条件1 THEN 语句1;
ELSEIF 条件2 THEN 语句2;
...
【ELSE 语句n;】
END IF;

# 示例
DELIMITER $
CREATE FUNCTION my_func(score INT) RETURNS CHAR
BEGIN
	IF score BETWEEN 90 AND 100 THEN RETURN 'A';
	ELSEIF score BETWEEN 80 AND 90 THEN RETURN 'B';
	ELSEIF score BETWEEN 60 AND 80 THEN RETURN 'C';
	ELSE RETURN 'D';
	END IF;
END $
DELIMITER ;
SELECT my_func(78);
```

## 循环结构

> 循环结构分类
>
> + while循环
> + loop循环
> + repeat循环
>
> 循环控制：
>
> + iterate类似于continue，即结束本次循环进入下一次循环
> + leave类似于break，跳出循环即结束当前循环
>
> 循环结构只能放在BEGIN END中

```mysql
# while循环语法【加上标记就可以搭配循环控制关键字使用了】
[标记:]WHILE 循环条件 DO
	 循环体;
END WHILE[标记];

# loop循环语法【没有循环条件，可以用来模拟简单的死循环，所以为了避免死循环只能搭配leave使用】
[标记:]LOOP
	循环体;
END LOOP[标记];

# repeat循环语法【类似于do..while循环】
[标记:]REPEAT
	循环体;
UNTIL 结束循环的条件
END REPEAT[标记];

# while循环结构的演示【批量插入，根据次数插入到admin表中多条记录】
DELIMITER $
CREATE PROCEDURE my_proInsert(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0; #定义局部变量用于自增
	WHILE i<insertCount DO 
		INSERT INTO admin(username,`password`) VALUES('aaa','123456789');
		SET i=i+1;
	END WHILE;
END $
DELIMITER ;
CALL my_proInsert(100);

# while循环结构搭配leave的演示【批量插入20次则结束循环】
DELIMITER $
CREATE PROCEDURE my_proInsert2(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	aa:WHILE i<insertCount DO 
		INSERT INTO admin(username,`password`) VALUES('aaa','123456789');
		SET i=i+1;
		IF i = 20 THEN LEAVE aa;
		END IF;
	END WHILE;
END $
DELIMITER ;
CALL my_proInsert2(100);

# while循环结构搭配iterate的演示【批量插入20条偶数次记录】
DELIMITER $
CREATE PROCEDURE my_proInsert3(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	aa:WHILE i<insertCount DO 
		SET i=i+1;
		IF MOD(i,2) <> 0 THEN ITERATE aa;
		END IF;
		INSERT INTO admin(username,`password`) VALUES(CONCAT('aaa',i),'123456789');
	END WHILE;
END $
DELIMITER ;
CALL my_proInsert3(100);
```

$$
\sqcap_{SNO}(\sigma_{JNO='J1'}(SPJ))
$$

$$
\sqcap_{SNO}(\sigma_{(JNO='J1') \and(PNO='P1')}(SPJ))
$$

$$
\sqcap_{SNO}(\sigma_{(JNO='J1') \and(COLOR='红' \and ┐(PNO=NULL)}(SPJ⋊ P))
$$

$$
\sqcap_{JNO}(SPJ) - \sqcap_{JNO}(\sigma_{(CITY='天津') \and(color='红')}(SPJ⋊ P ⋊ S))
$$

