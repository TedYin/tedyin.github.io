---
title: SQLite Study
layout: post
guid: Elt4Zo1Ybnle
date: 2014-06-03 22:39:00
tags:
---

<center>SQLite学习
===

##SQLite简介
SQLite是一个小型数据库，它被集成到Android系统中完成数据库部分。它不需要进行配置和管理，可以在Android系统中直接使用。

##SQLite使用

###1.SQLiteOpenHelper
SQLiteOpenHelper使用一个帮助类，通过继承它并实现onCreate方法和Upgrade方法，来管理我们的数据库。

+ onCreate方法是framework层调用，当你要打开某个数据库，但是这个数据库不存在时，framework就会调用这个方法去创建一个数据库。
+ onUpgrade方法是在对数据库进行更新时调用的，当你的数据库版本增加时，你可以通过调用这个方法修改数据库或者删除数据库然后通过调用onCreate方法重现创建数据库。

另外SQLiteOpenHelper还提供了getReadableDatabase方法和getWriteableDatabase方法去获取一个SQLDatabase对象。
上述两方法中都会使用SQLiteDatabase对象作为参数，这个类就代表了数据库。

**注意：**对于数据库创建的最佳实践是，每个表都建一个类，并且在这个类中定义*`static`*的onCreate和onUpgrade方法，这些方法在SQLiteOpenHelper中被调用，这样你的SQLiteOpenHelper实现类就有很好的可读性了，即使你有很多的数据表。

###2.SQLiteDatabase
`SQLiteDatabase`类是整个android数据库的基础类，它提供了对数据库操作的增删改查方法，另外还可以通过`execSQL`方法来执行SQL语句。
`ContentValues`是对key/value的一个包装，使用它可以将要插入或者要修改的数据以key/value的形式进行封装，在使用相应增改方法的时候直接使用。

+ rawQuery 是SQLDatabase的方法，他可以使用SQL语句直接查询。
+ query是推荐的查询方法，它的使用类似于Hibernate中的查询，只需要将参数查询条件等一参数的形式提供，就可以得到结果
+ Cursor对象，这个是查询的结果使用它可以遍历整个查询出的数据内容（table）
+ SQLiteBuilder是用来创建查询语句的

###3.Cursor
这个类是查询结果的封装，使用他可以很方便的获取数据库查询结果。

+ getCount()返回结果数据的条数
+ moveFirst 将游标指向第一条查询结果
+ moveNext 将游标指向下一条查询结果
+ isAfterLast 检查是否已经到达end

**注意：**`Cursor`在使用完成后需要调用close关闭。（TDOO：如果不关闭怎么办）


###4.DAO在Android中的使用

DAO的作用就是对外提供增删改查接口，在他的内部封装了SQLiteDatabase，以及SQLiteOpenHelper，将常用的增删改查以静态方法的形式对外提供。并且还封装了一些对数据进行初步加工的方法。DAO的存在就是将应用层和数据持久层相互隔离，应用层需要操作数据时，只通过DAO来和持久层交互。在持久层的实现中可以引入CursorLoader之类的东西。在Android中的最佳实践是使用ContentProvider来访做DAO与数据层进行交互，因为Android为此提供了安全而完善的机制访问数据层。

##SQLite使用注意事项

+ 在对数据库进行操作的时候会访问文件系统，很有可能是耗时操作，因此建议放在异步线程进行数据库操作。
+ 应用创建的数据库的位置为DATA/data/包名/databases/数据库名，其中DATA是Environment.getDataDirectory() 方法返回的路径。