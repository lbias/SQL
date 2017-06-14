# 关系型数据库的特性二: SQL 标准语法
关系型数据库都支援使用一种叫做 SQL (Structured Query Language) 的结构化查询语言。我们会用这种语法来操作数据库，例如：
```
 INSERT INTO events VALUES ("RubyConf", 100);```
这个 SQL 句会插入一笔数据到 `events` 表。
```
 SELECT * from events;```
这个 SQL 句告诉数据库拿出 events 表的所有数据。在 Rails 之中虽然我们没有直接撰写 SQL 句子，其实是 Rails 内部是自动帮我们转换好了，例如刚刚的句子，我们是写这样的 Ruby 代码：
```
 Event.create( :name => "RubyConf", :capacity => 100)```
和
```
 Event.all```
在 Rails 内部则会自动转换成 SQL 句，来向数据库进行操作。在稍后的章节我们会详细介绍这个 SQL 语言，你必须进一步了解 SQL 语言，才能够完整了解 Rails 是如何操作数据库的。
