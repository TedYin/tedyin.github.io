---
title: SQL约束
layout: post
date: 2014-08-24 21:17:00
---

SQL Constraints ：

对于XXX约束进行添加删除，修改的操作模式一般如下：

添加约束：
CREATE TABLE xxxTable(
字段1 字段类型 约束类型(PRIMARY KEY等)
···
字段N 字段类型 ···
)


CREATE TABLE xxxTable(
字段1 字段类型 CONSTRAINT 约束名称 约束类型(目标约束字段1，目标约束字段2...目标约束字段N)
···
字段N 字段类型 ···
)

修改约束：
ALERT TABLE xxxTable 
ADD 约束类型 （目标约束字段1，目标2...目标N）


ALERT TABLE xxxTable
ADD CONSTRAINT 约束名称 约束类型(目标约束字段1，目标2...目标N)

删除约束：

ALERT TABLE xxxTable
DROP 约束类型 约束名称          MySQL

ALERT TABLE xxxTable
DROP CONSTRAINT 约束名称    其他SQL的操作


1. NOT NULL ：强制约束字段不能为NULL

2. UNIQUE：唯一标识一个元组，一个表中可以多个UNIQUE约束的字段
如果需要为多个列定义UNIQUE约束可以这样操作：

CREATE TABLE Persons
(
Id_P int NOT NULL,
LastName varchar(255) NOT NULL,
FirstName varchar(255),
Address varchar(255),
City varchar(255),
CONSTRAINT uc_PersonID UNIQUE (Id_P,LastName)
)

如果一个表已经被创建，但是需要在某个字段上加上UNIQUE约束，操作如下：
ALERT TABLE tableName ADD UNIQUE (字段名称)

如果需要给添加的UNIQUE命名，并且为多个列加上UNIQUE约束，操作如下：
ALERT TABLE tableName ADD CONSTRAINT 约束名称 UNIQUE (字段1，字段2...字段N)
注意：约束名称的命名规则一般是，uq_字段名 或者 uq_组名

撤销UNIQUE：
MySql ：       ALERT TABLE tableName DROP INDEX 约束名
其他SQL：    ALERT TABLE tableName DROP CONSTRAINT 约束名 

3. PRIMARY KEY: 一个表中的主键，会自动生成一个UNIQUE约束，一个表中只能有一个主键
PRIMARY KEY 主键约束，每一个表中只能主键，而且有且仅有一个主键，主键列的值不能为NULL。一个表中可以有多个字段一起组成一个主键，但是主键只能有一个。添加删除主键的方式类似于添加或删除UNIQUE约束。


ALTER TABLE Persons
ADD CONSTRAINT pk_PersonID PRIMARY KEY (Id_P,LastName)


ALTER TABLE Persons
ADD  PRIMARY KEY (Id_P)


ALTER TABLE Persons
DROP CONSTRAINT pk_PersonID 


ALTER TABLE Persons
DROP PRIMARY KEY

4. FOREIGN KEY: 外键，一个表中的FOREIGN KEY指向另一个表中的PRIMARY KEY。

FOREIGN KEY用于预防破坏表之间连接的操作，也可约束非法插入的数据。生成或删除FROGIN KEY的方法与生成其他约束的方法相同。

5. CHECK：自检约束，如果某列或者某几列定义了该约束，那么在对该表进行操作的时候，这列或者这几列会检查操作是否符合他们各自的约束条件，如果符合约束条件那么允许操作，否则操作失败。

6. DEFAULT:该约束会向列中插入默认值，如果列没有其他的值，那么会将默认值加入到该列中。

创建表时加入默认值：

CREATE TABLE Orders
(
     Id_O int NOT NULL,     
     OrderNo int NOT NULL,
     Id_P int,
     OrderDate date DEFAULT GETDATE()
)


向以经存在的表加入默认值：

ALTER TABLE Persons
ALTER City SET DEFAULT ‘ShangHai'

删除默认值：

ALTER TABLE Persons
ALTER City DROP DEFAULT