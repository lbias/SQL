# SQL 语言: DML
操作数据的 SQL 就是 DML(Data Manipulation Language)，也就是做 CRUD 的操作。

## 新增资料

以下 SQL 会新增数据：

`INSERT INTO events (capacity, name) VALUES (200, "JSConf");`

> 这个对应的 Rails 语法是 `Event.create( :capacity => 200, :name => "JSConf")`

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fglqfjvgdfj30u00icmz6.jpg)

插入多笔 `INSERT INTO events (capacity, name) VALUES (300, "COSCUP"), (300, "OSDC.TW");`

接下来你也可以在 GUI 的视窗中，练习输入 SQL 句：

![](https://ws1.sinaimg.cn/large/006tNbRwgy1fglqg8e6m2j30u00ic0uu.jpg)

## 查询资料

捞全部 events 资料 `SELECT * FROM events;`

> 对应的 Rails 语法是 `Event.all`

只捞出指定的字段 SELECT `name, capacity FROM events;`

> 对应的 Rails 语法是 `Event.select(:name, :capacity).all`

字段前段可以补上表的名称 `SELECT events.name, events.capacity FROM events;`

排序 `SELECT name, capacity FROM events ORDER BY created_at;`

分页 `SELECT name, capacity FROM events ORDER BY capacity DESC, name ASC LIMIT 20 OFFSET 20;`

捞第一笔 `SELECT * FROM events ORDER BY id LIMIT 1`

>对应的 Rails 语法是 `Event.order("id").first`

## 修改资料

以下 SQL 会修改数据

`UPDATE events SET capacity=10;` 这会修改 events table 的所有数据把 capacity 改成 10

>对应的 Rails 语法是 `Event.update_all( :capacity => 100 )`

`UPDATE events SET capacity=100 WHERE name="RubyConf";` 用 WHERE 可以指定只有修改 name 是 "RubyConf" 的数据

>对应的 Rails 语法是 `Event.where( :name => "RubyConf" ).update_all( :capacity => 100)`

`UPDATE events SET capacity=100, name="RubyConf 2015" WHERE name="RubyConf";` 修改 capacity 和 name

>对应的 Rails 语法是 `Event.where( :capacity => 100, :name => "RubyConf" ).update_all( :capacity => 100)`

在 Rails 中，比较常见只修改一笔，例如：
```
@event = Event.find(123)
@event.update( :capacity => 200)
```
对应的 SQL 会是
```
SELECT * FROM events WHERE id = 123;
UPDATE events SET capacity=200 WHERE id=123;
```

## 删除数据数据

以下 SQL 会删除数据

`DELETE FROM events;` 会全部删除

> 对应的 Rails 语法是 `Event.delete_all`

`DELETE FROM events WHERE name="RubyConf";` 只删除

> 对应的 Rails 语法是 `Event.where( :name => "RubyConf" ).delete_all`

在 Rails 中，比较常见只删除一笔，例如：
```
@event = Event.find(123)
@event.destroy
```

对应的 SQL 会是
```
SELECT * FROM events WHERE id = 123;
DELETE FROM events WHERE id = 123;
```

## 查有哪些 tables 和 columns

各家语法不一样：

* SQLite3: `.tables` 和 `.schema tablename`
* MySQL: `show tables` 和 `describe tablename`
* PostgreSQL: `\dt` 和 `\d tablename`

这些查询在 Rails 启动的时后，其实也会帮我们做。你可以在 rails console 中对 model 执行 `columns` 方法，例如 `Event.columns` 就会反射出这个表有哪些字段。

## 条件查询

以下是一些范例来做条件查询：

* `SELECT * FROM events WHERE date = '2015-03-15';`
* 条件或 `SELECT * FROM events WHERE date = '2015-03-15' OR date = '2015-03-16';`
* 某个区间 `SELECT * FROM events WHERE date BETWEEN '2015-03-15' AND '2015-03-30';`
* 条件且 `SELECT * FROM events WHERE date = '2015-03-15' AND capacity >= 100;`
* 模糊比对 `SELECT * FROM events WHERE name LIKE '%Ruby%';`
* 不可为空 `SELECT * FROM events WHERE description IS NOT NULL;`

条件比对时，小心大小写(Case insensitive)不同数据库默认不同。MySQL 是不分大小写(case insensitive)、PostgreSQL 会区分大小写(case sensitive)。

## Indexes 索引

`WHERE`、`ORDER` 条件字段最好都要加上数据库索引(Index)，例如范例中的 date 字串，如果没有索引的话，会是 O(n) 的效率(这里又叫作 Full Table Scan，需要扫过整个表的意思)，数据库越多数据会越慢。如果有索引的话，会是 O(logn)，在数据量大的情况差非常多。

模糊搜寻 LIKE 查询都会变成 Full Table Scan，没办法用数据库索引，百宝箱教过的 ransack gem 搜寻是用 LIKE 语法，在几万笔数据内效能还能接受，再大的数据量就需要用另外的 Full-Text Searching 引擎了，例如 ElasticSearch。

加索引的 SQL 语法：

加索引 `CREATE INDEX events_user_id_idx ON events(user_id);`
索引并且值是唯一 `CREATE UNIQUE INDEX xxx_idx ON xxx(yyy);`
在 Rails Migration 中要加上索引的话，可用 `add_index` 语法，例如 `add_index :events, :date`。

> 将字段设成 unique 跟设成 unique index 是一样的

当然也不是所有字段通通都加上索引就好了，因为加索引会让写入数据变慢(因为要建立索引，也会增加储存空间)，但是查询时会加快。

## 练习
假设我们有一个 Table 叫做 people，里面有字段 id, first_name, last_name, gender (值可能是 M 或 F), birthday
1. 请写出 SQL 捞出所有资料
1. 请写出 SQL 捞出所有男生
1. 请写出 SQL 插入一笔资料
1. 请写出 SQL 更新 id 为 3 的资料，将姓名修改成你的名字
1. 请写出 SQL 删除 id 为 4 的资料
