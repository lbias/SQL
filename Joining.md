# Joining
SQL 查询厉害的地方，就是可以同时关联(Joining)多张表来进行复杂的查询。让我们先准备示范用的数据。

以下是 user 一对多 events 的情境，请执行 `sqlite3 demo2.db`，并输入以下 SQL 建立 tables 和数据：
```
  CREATE TABLE events (id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, name TEXT, capacity INTEGER, user_id INTEGER);
  CREATE TABLE users (id INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT, name TEXT);
  INSERT INTO users (name) VALUES ('ihower');
  INSERT INTO users (name) VALUES ('john');
  INSERT INTO users (name) VALUES ('roy');
  INSERT INTO events (name, capacity, user_id) VALUES ('rubyconf',100, 1);
  INSERT INTO events (name, capacity, user_id) VALUES ('jsconf', 200, 1);
  INSERT INTO events (name, capacity, user_id) VALUES ('cssconf', 150, 2);
  INSERT INTO events (name, capacity, user_id) VALUES ('htmlconf', 300, NULL);
  ```

![](https://ws1.sinaimg.cn/large/006tNc79gy1fgpbsfb04zj30u00h9di6.jpg)

跨 Tables 进行 Joining 查询，常用的有 Inner Joining 和 Left Outer Joining 两种：

## Inner joining 合并两张 tables，接不起来就不要：

捞出所有活动，以及该活动的主办人资料：

`SELECT * FROM events INNER JOIN users ON events.user_id = users.id;`
或 `SELECT * FROM events, users WHERE events.user_id = users.id;`

![](https://ws3.sinaimg.cn/large/006tNc79ly1fgpbtivw7aj30u00ki76u.jpg)

对应的 Rails 语法是 `User.joins(:events)`

## Outer joining 合并两张 tables，接不起就填 NULL:

捞出所有活动，以及该活动的主办人资料(包括没有主办人的活动)：

`SELECT * FROM events LEFT OUTER JOIN users ON events.user_id = users.id;`

![](https://ws2.sinaimg.cn/large/006tNc79ly1fgpbuibcy7j30u00kitbd.jpg)

## AS 语法

因为有多张 tables 在 SQL 时，column 最好必须加上 table name 当作 prefix (特别是有重复的 column name 时，在 WHERE 条件里可能会无法判断)，而且可以加上别名 AS。

例如 `SELECT events.id AS event_id, events.capacity AS ec, events.name FROM events INNER JOIN users ON events.user_id = users.id WHERE ec=100;`

## Rails 的 includes 原理

Rails ActiveRecord 的 includes 方法(当没有 WHERE 过滤条件时)则采用另一种 SQL 策略来作 Outer joining:

例如 `Event.includes(:user)` 这个 Rails 语法，实际上的 SQL 是：

`SELECT * FROM events;`

然后透过程式组合出所有的 user_id 成为 (1, 2, 3, 5, 9, 11, 14)，然后再

`SELECT * FROM users WHERE users.id IN (1, 2, 3, 5, 9, 11, 14);`

不过，如果有 users 身上有 WHERE 条件的话，Rails 会变成 LEFT OUTER JOIN。

## Joining 图表

![](https://ws1.sinaimg.cn/large/006tNc79ly1fgpbvxq1yij30qu0l4jtr.jpg)

这张 google 比较常看到，出处 [Visual Representation of SQL Joins](https://www.codeproject.com/Articles/33052/Visual-Representation-of-SQL-Joins)

![](https://ws4.sinaimg.cn/large/006tNc79ly1fgpbx90hw5j30ie0dsgml.jpg)

这张更清楚说明不同 Join 差别，出处 [SQL Joins Better](https://blog.jooq.org/2016/07/05/say-no-to-venn-diagrams-when-explaining-joins/)
