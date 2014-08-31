---
title: MySQL添加外键
date: 2014-08-31 11:19:20
layout: post
---

##什么是外键
外键（Foreign Key），是SQL中的一种约束。假设有一个表A，外键约束的是它和别的表之间的参照关系，假如A表中的某个字段（或者某几个字段）是别的表中的数据，如果别的表没有这个数据A中的相应字段也不能有这些数据，那么A表中的这几个字段和别的表中的这几个字段就存在某种约束，这种约束就称为外键。


##添加外键的步骤
假设有A、B两个表，A中的aid的取值必须在B中的bid的某个值，即aid references bid
###1. 需要在参考字段(aid)和被参考字段(bid)，之间建立索引
```
alter table A add index IDX_aid (aid); ——为A的aid字段添加索引
alter table B add index IDX_bid (bid); ——为B的bid字段添加索引
```
###2. 建立好索引之后，为A表添加到B表的外键约束

```
—— 添加约束
alter table A add constraint FK_aid_bid
foreign key (aid)
references B.bid;
```
这样就建立好了aid到bid的外键，aid的值只能是bid的值中某个，不可能是别的

##级联操作
上述约束建立好了，如果bid发生了变化，我么也希望aid跟着一起变化，那怎么办呢？这个“一起变化”的希望，实际上就是一个级联操作。可以通过`on update cascade`这句约束来实现。


```
—— 添加约束
alter table A add constraint FK_aid_bid
foreign key (aid)
references B(bid)
on update cascade;
```
+ 加入`on update cascade`之后将会对aid进行同步更新。除`cascade`之外，我们还可以通过`restrict`来禁止在bid发生变化时也使得aid发生变更，我们还可以使用`set null`约束，来使得在bid发生变更后将相应的aid置为null。

+ 除了可以监控update的操作外，我们还可以使用`on delete [cascade | ···]`作为条件判断，当发生上面的条件时，可以使用`cascade`、`restrict`、`set null`等对外键进行操作。

##删除外键
###指定外键名称时删除外键
如果现在aid不需要再参考bid了，我们可以删除掉这个约束。
上面在定义外键的时候，给这个外键取了个名字`FK_aid_bid`，因此我们可以通过这个外键的名字来删除外键。
```
alter table A drop foreign key FK_aid_bid;
```
这样就可以删除掉外键了，但是之前建立的索引不会被删除，如果要删除索引，需要自己手动删除。

###未指定外键名称时删除外键
如果在建立外键时并没有指定外键名称*eg: fk_symbol*，那怎么办？实际上如果没有指定外键名，MySql会默认加上这个外键名，获取这个默认外键名称的方法如下：
```
show create table tableName;
```
即可查看到该表的详细创建信息，里面就有MySql默认生成的外键名称，拿到该名称就可以删除该外键了。

##遇到的问题
在创建外键的过程中我遇到无法创建的问题，解决办法：
为了得到详细的错误原因，可以使用下面方法:
`SHOW ENGINE INNODB STATUS\G`来获取出错的详细信息。
常见的错误原因：
+ 没有建立索引
+ 外键(aid)和参考字段(bid)的值类型不一样
+ 创建外键的SQL有误

##参考
[添加外键](http://fykyx521.iteye.com/blog/428352)
[外键删除](http://database.51cto.com/art/201010/229146.htm)
