# `DDL`

`DDL`简单理解就是用来操作数据库，表等。

# `DDL`:操作数据库

操作数据库主要就是对数据库的增删查操作。

**查询所有的数据库**

```sqlite
SHOW DATABASES;
```

**创建数据库**

```sqlite
CREATE DATABASE 数据库名称;
CREATE DATABASE IF NOT EXISTS 数据库名称; --判断，如果不存在则创建
```

**删除数据库**

```sqlite
DROP DATABASE 数据库名称;
DROP DATABASE IF EXISTS 数据库名称; -- 判断，如果存在则删除
```

**使用数据库**

```sqlite
USE 数据库名称;
```

**查看当前使用的数据库**

```sqlite
SELECT DATABASE();
```

# `DDL`:操作表

操作表也就是对表进行增（`Create`）删（`Retrieve`）改（`Update`）查（`Delete`）。

**查询当前数据库下所有表名称**

```sqlite
SHOW TABLES;
```

**查询表结构**

```sqlite
DESC 表名称;
```

**创建表**

```sqlite
CREATE TABLE 表名 (
	字段名1 数据类型1,
	字段名2 数据类型2,
	…
	字段名n 数据类型n
);

-- 举例
create table tb_user (
	id int,
	username varchar(20),
	password varchar(32)
);
```

***注意：最后一行末尾，不能加逗号***

# 数据类型

`MySQL`支持多种类型，可以分为三类：

- 数值

```sqlite
tinyint : 小整数型，占一个字节
int ： 大整数类型，占四个字节
	例如 ： age int
double ： 浮点类型
	使用格式： 字段名 double(总长度,小数点后保留的位数)
	例如 ： score double(5,2)
```

-   日期
```sqlite
date ： 日期值。只包含年月日
	例如 ：birthday date ：
datetime ： 混合日期和时间值。包含年月日时分秒
```

- 字符串
```sqlite
char ： 定长字符串。
	优点：存储性能高
	缺点：浪费空间
	例如 ： name char(10) 如果存储的数据字符个数不足10个，也会占10个的空间
varchar ： 变长字符串。
	优点：节约空间
	缺点：存储性能底
	例如 ： name varchar(10) 如果存储的数据字符个数不足10个，那就数据字符个数是几就占几个的空间
```

# 案例

**需求：设计一张学生表，请注重数据类型、长度的合理性**

1. 编号
2. 姓名，姓名最长不超过10个汉字
3. 性别，因为取值只有两种可能，因此最多一个汉字
4. 生日，取值为年月日
5. 入学成绩，小数点后保留两位
6. 邮件地址，最大长度不超过 64
7. 家庭联系电话，不一定是手机号码，可能会出现 - 等字符
8. 学生状态（用数字表示，正常、休学、毕业...）

**语句设计如下：**

```sqlite
create table student (
	id int,
	name varchar(10),
	gender char(1),
	birthday date,
	score double(5,2),
	email varchar(15),
	tel varchar(15),
	status tinyint
);
```

**删除表**

```sqlite
DROP TABLE 表名;
DROP TABLE IF EXISTS 表名; -- 删除表时判断表是否存在
```

**修改表名**

```sqlite
ALTER TABLE 表名 RENAME TO 新的表名;

-- 将表名student修改为stu
alter table student rename to stu;
```

**添加一列**

```sqlite
ALTER TABLE 表名 ADD 列名 数据类型;

-- 给stu表添加一列address，该字段类型是varchar(50)
alter table stu add address varchar(50);
```

**修改数据类型**

```sqlite
ALTER TABLE 表名 MODIFY 列名 新数据类型;

-- 将stu表中的address字段的类型改为 char(50)
alter table stu modify address char(50);
```

**修改列名和数据类型**

```sqlite
ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型;

-- 将stu表中的address字段名改为 addr，类型改为varchar(50)
alter table stu change address addr varchar(50);
```

**删除列**

```sqlite
ALTER TABLE 表名 DROP 列名;

-- 将stu表中的addr字段 删除
alter table stu drop addr;
```